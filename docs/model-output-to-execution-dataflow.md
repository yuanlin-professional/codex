# 研究：模型输出 → 执行指令的完整数据流

## Context

本文档详细描述 `codex-rs/core` 中，模型 API 返回的输出如何被转换为具体的执行指令（如 shell 命令执行、文件写入等）。

---

## 完整数据流概览

```
Model API (ResponseStream)
    ↓ ResponseEvent::OutputItemDone(ResponseItem)
[回合主循环] session/turn.rs → stream_events_utils.rs
    ↓ ToolRouter::build_tool_call(ResponseItem)
ToolCall { tool_name, call_id, payload }
    ↓ 入队 in_flight FuturesOrdered
[并行执行层] tools/parallel.rs
    ↓ ToolCallRuntime::handle_tool_call()
[路由层] tools/router.rs → tools/registry.rs
    ↓ registry.dispatch_any_with_terminal_outcome()
[Handler 层] tools/handlers/shell/shell_command.rs 等
    ↓ handler.handle(ToolInvocation)
[编排层] tools/orchestrator.rs
    ↓ 审批 → 沙箱选择 → 执行
[运行时层] tools/runtimes/shell.rs → exec.rs → spawn.rs
    ↓ spawn_child_async()
[OS 进程] 实际 shell 命令执行
    ↓ ExecToolCallOutput { stdout, stderr, exit_code, ... }
[结果转换] AnyToolResult → ResponseInputItem::FunctionCallOutput
    ↓ record_conversation_items()
[历史记录] 写入会话历史，提交给模型下一轮
```

---

## 阶段详解

### 阶段 1: 模型流式响应接收

**关键文件**: `codex-rs/core/src/client_common.rs` (L68-87)

```rust
// ResponseEvent 从 codex_api 重导出
pub use codex_api::ResponseEvent;

pub struct ResponseStream {
    pub(crate) rx_event: mpsc::Receiver<Result<ResponseEvent>>,
    pub(crate) consumer_dropped: CancellationToken,
}

impl Stream for ResponseStream {
    type Item = Result<ResponseEvent>;
    fn poll_next(mut self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Option<Self::Item>> {
        self.rx_event.poll_recv(cx)
    }
}
```

- `ResponseStream`: 封装 `mpsc::Receiver<Result<ResponseEvent>>`，实现 tokio Stream trait
- `ResponseEvent` enum: 从 `codex_api` crate 重导出，包含 `Created`, `OutputItemDone`, `OutputItemAdded`, `OutputTextDelta` 等变体
- `ResponseItem` enum: 模型输出的强类型表示，包括 `FunctionCall`, `Message`, `Reasoning`, `CustomToolCall` 等

### 阶段 2: 回合主循环消费事件

**关键文件**: `codex-rs/core/src/session/turn.rs`

- `run_sampling_request()` (L915-924): 回合入口函数
- `try_run_sampling_request()` (L1712+): 实际执行流式消费

```rust
// L1750-1751: 创建有序 Future 队列
let mut in_flight: FuturesOrdered<BoxFuture<'static, CodexResult<ResponseInputItem>>> =
    FuturesOrdered::new();

loop {
    let event = stream.next().or_cancel(&cancellation_token).await;
    match event {
        ResponseEvent::OutputItemDone(item) => {
            // L1891: 处理完成的输出项
            let output_result = handle_output_item_done(&mut ctx, item, ...).await;
            if let Some(tool_future) = output_result.tool_future {
                in_flight.push_back(tool_future);  // 入队异步执行
            }
        }
        // ... 其他事件类型
    }
}
// L2164: 等待所有工具完成
drain_in_flight(&mut in_flight, sess.clone(), turn_context.clone()).await?;
```

### 阶段 3: 工具调用提取 — ResponseItem → ToolCall

**关键文件**:
- `codex-rs/core/src/stream_events_utils.rs` — `handle_output_item_done()` (L342-453)
- `codex-rs/core/src/tools/router.rs` — `ToolRouter::build_tool_call()` (L89-137)

**handle_output_item_done 返回类型** (L247-252):
```rust
pub(crate) struct OutputItemResult {
    pub last_agent_message: Option<String>,
    pub needs_follow_up: bool,
    pub tool_future: Option<InFlightFuture<'static>>,
}
```

**build_tool_call 提取逻辑** (router.rs L89-137):
```rust
pub fn build_tool_call(item: ResponseItem) -> Result<Option<ToolCall>, FunctionCallError> {
    match item {
        ResponseItem::FunctionCall { name, namespace, arguments, call_id, .. } => {
            Ok(Some(ToolCall {
                tool_name: ToolName::new(namespace, name),
                call_id,
                payload: ToolPayload::Function { arguments },
            }))
        }
        ResponseItem::CustomToolCall { name, input, call_id, .. } => {
            Ok(Some(ToolCall {
                tool_name: ToolName::plain(name),
                call_id,
                payload: ToolPayload::Custom { input },
            }))
        }
        ResponseItem::ToolSearchCall { ... } => { /* 创建 "tool_search" 特殊工具调用 */ }
        _ => Ok(None),  // 非工具类输出跳过
    }
}
```

**核心类型** (router.rs L27-32):
```rust
#[derive(Clone, Debug)]
pub struct ToolCall {
    pub tool_name: ToolName,       // 如 "shell_command", "apply_patch"
    pub call_id: String,           // 此次调用的唯一 ID
    pub payload: ToolPayload,      // Function{arguments} | Custom{input} | ToolSearch{...}
}
```

### 阶段 4: 并行执行与路由分发

**关键文件**: `codex-rs/core/src/tools/parallel.rs`

**handle_tool_call()** (L62-79):
```rust
pub(crate) fn handle_tool_call(
    self,
    call: ToolCall,
    cancellation_token: CancellationToken,
) -> impl std::future::Future<Output = Result<ResponseInputItem, CodexErr>>
```

内部流程:
1. 调用 `handle_tool_call_with_source()` (L82-87)，使用 `ToolCallSource::Direct`
2. 使用 `RwLock` 控制并行：支持并行的工具用 read lock，不支持的用 write lock
3. 调用 `router.dispatch_tool_call_with_terminal_outcome()` (L120-132)

**关键文件**: `codex-rs/core/src/tools/router.rs` (~L164-184)

- 将 `ToolCall` 包装为 `ToolInvocation`（附加 session、turn context、cancellation token）
- 委托 `ToolRegistry::dispatch_any_with_terminal_outcome()`

### 阶段 5: 注册表分发与 Hook 拦截

**关键文件**: `codex-rs/core/src/tools/registry.rs`

**AnyToolResult 结构** (L110-115):
```rust
pub(crate) struct AnyToolResult {
    pub(crate) call_id: String,
    pub(crate) payload: ToolPayload,
    pub(crate) result: Box<dyn ToolOutput>,
    pub(crate) post_tool_use_payload: Option<PostToolUsePayload>,
}
```

**dispatch_any_with_terminal_outcome()** (L326-601) 执行流水线:

```
1. 注册表查找 — self.tool(&tool_name) 返回 Arc<dyn CoreToolRuntime>
2. Pre-tool hooks (L416-460) — run_pre_tool_use_hooks()
   ├── Blocked → 提前返回
   └── Updated → 修改输入
3. 工具执行 (L466-490) — handle_any_tool() + telemetry logging
4. Post-tool hooks (L504-548) — run_post_tool_use_hooks()
   └── 可提供反馈或停止执行
5. 生命周期通知 (L550-569) — notify_tool_finish()
```

**核心 trait**:
```rust
#[async_trait]
trait ToolExecutor<T> {
    fn tool_name(&self) -> ToolName;
    fn spec(&self) -> Option<ToolSpec>;
    async fn handle(&self, invocation: T) -> Result<Box<dyn ToolOutput>>;
}
```

### 阶段 6: 具体 Handler 执行（以 shell 为例）

**关键文件**: `codex-rs/core/src/tools/handlers/shell/shell_command.rs`

**ShellCommandHandler 结构** (L40-50):
```rust
pub struct ShellCommandHandler {
    backend: ShellCommandBackend,
    options: Option<ShellCommandHandlerOptions>,
}

pub(crate) struct ShellCommandHandlerOptions {
    pub(crate) backend_config: ShellCommandBackendConfig,
    pub(crate) allow_login_shell: bool,
    pub(crate) exec_permission_approvals_enabled: bool,
}
```

**ToolExecutor 实现** (L129-204):
```rust
#[async_trait::async_trait]
impl ToolExecutor<ToolInvocation> for ShellCommandHandler {
    async fn handle(&self, invocation: ToolInvocation) -> Result<Box<dyn ToolOutput>> {
        // 1. 解析 JSON 参数 → ShellCommandToolCallParams (L170)
        let params: ShellCommandToolCallParams = parse_arguments(&arguments)?;

        // 2. 转换为 ExecParams (L181-187)
        let exec_params = Self::to_exec_params(&params, ...)?;

        // 3. 调用 run_exec_like() 进入编排层 (L189-202)
        let result = run_exec_like(...).await?;

        // 4. 返回工具输出
        Ok(Box::new(result))
    }
}
```

**Hook 支持** (L212-247):
- `pre_tool_use_payload()`: 提取命令供 hooks 检查
- `with_updated_hook_input()`: 重写 hook 修改后的命令
- `post_tool_use_payload()`: 准备执行后的 hook 数据

### 阶段 7: 编排器 — 审批 + 沙箱 + 执行

**关键文件**: `codex-rs/core/src/tools/orchestrator.rs`

**ToolOrchestrator 结构** (L42-44):
```rust
pub(crate) struct ToolOrchestrator {
    sandbox: SandboxManager,
}
```

**主流程 run()** (L128-138):
```rust
pub async fn run<Rq, Out, T>(
    &mut self,
    tool: &mut T,
    req: &Rq,
    tool_ctx: &ToolCtx,
    turn_ctx: &TurnContext,
    approval_policy: AskForApproval,
) -> Result<OrchestratorRunResult<Out>, ToolError>
```

**三阶段流水线** (L145-201):
```
ToolOrchestrator::run()
├── 1. 审批决策 (L145+)
│   ├── Skip（策略允许）
│   ├── Forbidden（策略禁止 → 返回错误）
│   └── NeedsApproval → 请求 Guardian/用户审批
├── 2. 沙箱选择（隐含在 sandbox attempt 中）
│   └── 根据策略选择 SandboxType (None/Landlock/Windows/...)
└── 3. 执行尝试 run_attempt() (L58-126)
    ├── begin_network_approval() (L68)
    ├── tool.run(request, sandbox_attempt, tool_ctx) (L97)
    └── finish_network_approval() (L106-125)
```

**失败处理**: 沙箱拒绝 & 可升级 → 请求重试审批 → 无沙箱重试

### 阶段 8: Shell 运行时 — 命令转换与实际执行

**关键文件**: `codex-rs/core/src/tools/runtimes/shell.rs`

**ShellRequest 结构** (L48-64):
```rust
#[derive(Clone, Debug)]
pub struct ShellRequest {
    pub command: Vec<String>,
    pub shell_type: Option<ShellType>,
    pub hook_command: String,
    pub cwd: AbsolutePathBuf,
    pub timeout_ms: Option<u64>,
    pub env: HashMap<String, String>,
    pub explicit_env_overrides: HashMap<String, String>,
    pub network: Option<NetworkProxy>,
    pub sandbox_permissions: SandboxPermissions,
    pub additional_permissions: Option<AdditionalPermissionProfile>,
    pub justification: Option<String>,
    pub exec_approval_requirement: ExecApprovalRequirement,
}
```

**后端选择** (L66-80):
```rust
pub(crate) enum ShellRuntimeBackend {
    ShellCommandClassic,
    ShellCommandZshFork,
}
```

**ToolRuntime 实现** (L200-260):
```rust
async fn run(
    &mut self,
    req: &ShellRequest,
    attempt: &SandboxAttempt<'_>,
    ctx: &ToolCtx,
) -> Result<ExecToolCallOutput, ToolError> {
    // 1. 获取用户 shell 类型
    let session_shell = ctx.session.user_shell();

    // 2. 管理网络策略
    let managed_network = managed_network_for_sandbox_permissions(...);

    // 3. 构建执行环境
    let env = exec_env_for_sandbox_permissions(...);

    // 4. 包装命令（加 shell -c 前缀、snapshot 等）
    let command = maybe_wrap_shell_lc_with_snapshot(&req.command, ...);

    // 5. 禁用 PowerShell profiles（如适用）
    // 6. 应用 UTF-8 编码（如适用）

    // 7. 最终执行 → spawn_child_async()
    let out = execute_env(env, Self::stdout_stream(ctx)).await?;
}
```

**关键文件**: `codex-rs/core/src/spawn.rs` (L51-126)

```rust
pub(crate) struct SpawnChildRequest<'a> {
    pub program: PathBuf,
    pub args: Vec<String>,
    pub arg0: Option<&'a str>,
    pub cwd: AbsolutePathBuf,
    pub network_sandbox_policy: NetworkSandboxPolicy,
    pub network: Option<&'a NetworkProxy>,
    pub stdio_policy: StdioPolicy,
    pub env: HashMap<String, String>,
}

pub(crate) async fn spawn_child_async(request: SpawnChildRequest<'_>) -> std::io::Result<Child> {
    // 创建 Command
    // 设置 args, cwd, env
    // 设置 CODEX_SANDBOX_NETWORK_DISABLED (如需)
    // Unix: 进程组 + parent death signal
    // 配置 stdio policy (redirect vs inherit)
    // 产生子进程 kill_on_drop(true)
}
```

### 阶段 9: 结果回传模型

**关键文件**: `codex-rs/core/src/tools/context.rs` (L307-340)

```rust
// ExecCommandToolOutput — 对 ExecToolCallOutput 的高层封装
#[derive(Debug, Clone, PartialEq)]
pub struct ExecCommandToolOutput {
    pub event_call_id: String,
    pub chunk_id: String,
    pub wall_time: Duration,
    pub raw_output: Vec<u8>,
    pub truncation_policy: TruncationPolicy,
    pub max_output_tokens: Option<usize>,
    pub process_id: Option<i32>,
    pub exit_code: Option<i32>,
    pub original_token_count: Option<usize>,
    pub hook_command: Option<String>,
}

impl ToolOutput for ExecCommandToolOutput {
    fn to_response_item(&self, call_id: &str, payload: &ToolPayload) -> ResponseInputItem {
        function_tool_response(
            call_id,
            payload,
            vec![FunctionCallOutputContentItem::InputText {
                text: self.response_text(),
            }],
            Some(true),
        )
    }
}
```

**AnyToolResult 转换** (registry.rs L118-126):
```rust
impl AnyToolResult {
    pub(crate) fn into_response(self) -> ResponseInputItem {
        let Self { call_id, payload, result, .. } = self;
        result.to_response_item(&call_id, &payload)
    }
}
```

**最终形式**: `ResponseInputItem::FunctionCallOutput` 或 `ResponseInputItem::CustomToolCallOutput`

**写入历史** (session/mod.rs L2529-2535):
```rust
pub(crate) async fn record_conversation_items(
    &self,
    turn_context: &TurnContext,
    items: &[ResponseItem],
) {
    self.record_into_history(items, turn_context).await;        // 写入内存历史
    self.persist_rollout_response_items(items).await;           // 持久化
    self.send_raw_response_items(turn_context, items).await;    // 发送事件给客户端
}
```

结果通过 `record_conversation_items()` 写入历史，模型下一轮可以看到。

---

## 关键类型一览

| 阶段 | 类型 | 所在文件 | 用途 |
|------|------|---------|------|
| 流接收 | `ResponseEvent` | codex_api (re-export in client_common.rs) | 模型流式事件 |
| 解析 | `ResponseItem` | codex-protocol | 强类型模型输出（FunctionCall/Message/...） |
| 提取 | `ToolCall` | tools/router.rs:27 | 工具调用元数据 |
| 路由 | `ToolInvocation` | tools/context.rs | 完整执行上下文 |
| Handler | `ToolPayload` | tools/router.rs | 参数载体（Function/Custom/ToolSearch） |
| 编排 | `ToolOrchestrator` | tools/orchestrator.rs:42 | 审批+沙箱+执行流水线 |
| 执行结果 | `ExecToolCallOutput` | exec.rs:724 | Shell 执行原始结果 |
| 工具输出 | `ExecCommandToolOutput` | tools/context.rs:307 | 高层工具输出（实现 ToolOutput trait） |
| 分发结果 | `AnyToolResult` | tools/registry.rs:110 | 包装 ToolOutput + call_id + payload |
| 回传 | `ResponseInputItem` | codex-protocol | 工具结果转为模型输入 |

---

## 关键文件索引

| 文件 | 核心职责 | 重要行号 |
|------|---------|---------|
| `session/turn.rs` | 回合状态机，驱动主循环 | L915 (入口), L1750 (FuturesOrdered), L2164 (drain) |
| `stream_events_utils.rs` | 事件处理入口，调用 build_tool_call | L342 (handle_output_item_done) |
| `tools/router.rs` | ToolCall 构建 + 路由分发 | L27 (ToolCall), L89 (build_tool_call), L164 (dispatch) |
| `tools/registry.rs` | Handler 注册 + dispatch + hook 拦截 | L110 (AnyToolResult), L326 (dispatch pipeline) |
| `tools/parallel.rs` | 并行执行 + 结果收集 | L62 (handle_tool_call) |
| `tools/orchestrator.rs` | 审批→沙箱→执行编排 | L42 (struct), L58 (run_attempt), L128 (run) |
| `tools/handlers/shell/shell_command.rs` | Shell 工具 handler | L40 (struct), L129 (impl) |
| `tools/handlers/apply_patch.rs` | 文件写入 handler | — |
| `tools/runtimes/shell.rs` | Shell 命令实际执行 | L48 (ShellRequest), L200 (run) |
| `tools/context.rs` | ToolOutput trait + ExecCommandToolOutput | L307 (struct), L322 (impl) |
| `event_mapping.rs` | ResponseItem→TurnItem（非工具类转换） | L136 (parse_turn_item) |
| `exec.rs` | 进程执行参数与入口 | L302 (process_exec_tool_call), L724 (finalize_exec_result) |
| `spawn.rs` | 子进程产生（平台无关） | L40 (SpawnChildRequest), L51 (spawn_child_async) |
| `session/mod.rs` | 会话管理，历史记录 | L2529 (record_conversation_items) |

---

## 设计注意事项

1. **FunctionCall 不经过 event_mapping** — `parse_turn_item()` 不处理工具调用，它们走独立管道（tools/parallel.rs → registry.rs）

2. **并行控制** — `RwLock` 区分可并行工具（read lock）和串行工具（write lock），实现细粒度并发

3. **编排器模式** — 每个 handler 内部创建 `ToolOrchestrator`，而非全局单一编排。这样每个工具可以有独立的审批和沙箱策略

4. **结果格式** — 工具输出最终转为 `ResponseInputItem::FunctionCallOutput` 或 `CustomToolCallOutput` 回传给模型

5. **ExecToolCallOutput vs ExecCommandToolOutput**:
   - `ExecToolCallOutput` (exec.rs): 底层原始执行结果，由 `finalize_exec_result()` 创建
   - `ExecCommandToolOutput` (tools/context.rs): 高层封装，实现 `ToolOutput` trait，负责格式化输出文本和转换为 `ResponseInputItem`

6. **ResponseEvent 来源** — `ResponseEvent` 不在 core 中定义，而是从 `codex_api` crate 重导出

7. **record_conversation_items 三步走**:
   - `record_into_history()` — 写入内存历史
   - `persist_rollout_response_items()` — 持久化到磁盘
   - `send_raw_response_items()` — 发送事件流给前端客户端
