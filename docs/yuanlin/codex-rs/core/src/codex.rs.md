# `codex.rs` — Codex 核心引擎：会话生命周期与 Turn 执行主循环

## 功能概述

`codex.rs` 是 Codex 系统的核心入口文件（约 7495 行），负责整个 AI 对话会话（Session）的创建、配置、Turn（轮次）执行、工具调用调度以及事件流管理。它定义了顶层公开接口 `Codex` 结构体，作为 Submission/Event 队列对模型进行操作；内部通过 `Session` 管理一个完整会话的状态，包括对话历史、MCP 连接、沙箱策略、审批流程、Guardian 审核、实时对话等。文件还包含 `submission_loop`（提交处理主循环）、`run_turn`（单轮次采样执行）、`run_sampling_request`（模型采样请求+重试+回退）以及大量的 handlers 模块（处理中断、审批、回滚、技能列表、关闭等操作）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Codex` | struct | 最高层接口，包含 Submission 发送通道、Event 接收通道、Session Arc 引用，提供 `spawn`/`submit`/`next_event`/`shutdown_and_wait` 等方法 |
| `CodexSpawnOk` | struct | `Codex::spawn` 的返回值，包含 `codex`、`thread_id` |
| `CodexSpawnArgs` | struct | 创建 Codex 实例所需的全部参数（config、auth、models、mcp、skills、plugins 等） |
| `Session` | struct | 会话上下文，持有 conversation_id、事件发送通道、状态 Mutex、特性开关、MCP 刷新状态、实时对话管理器、Guardian 审核、JS REPL 句柄等 |
| `SessionConfiguration` | struct | 会话配置快照，包含 provider、协作模式、审批策略、沙箱策略、cwd、用户/开发者指令等 |
| `SessionSettingsUpdate` | struct | 会话设置增量更新，可选更新 cwd、审批策略、沙箱策略、协作模式、个性化等 |
| `TurnContext` | struct | 单轮次上下文，包含模型信息、推理设置、工具配置、特性开关、沙箱/网络策略、cwd、截断策略、动态工具、技能/元数据/计时等 |
| `TurnSkillsContext` | struct | Turn 级别的技能上下文，包含技能加载结果和隐式调用已见技能集合 |
| `PreviousTurnSettings` | struct | 上一轮次设置（model、realtime_active），用于检测模型切换和实时状态变更 |
| `SteerInputError` | enum | Steer 输入错误类型：无活跃 Turn、Turn ID 不匹配、不可引导的 Turn 类型、空输入 |
| `SamplingRequestResult` | struct | 采样请求返回结果：是否需要后续请求、最后一条 Agent 消息 |
| `AssistantMessageStreamParsers` | struct | 流式解析器管理器，支持 citation 剥离和 plan 模式分段 |
| `PlanModeStreamState` | struct | Plan 模式流状态，管理待发射的 Agent 消息、plan item 生命周期 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Codex::spawn` | `async fn spawn(args: CodexSpawnArgs) -> CodexResult<CodexSpawnOk>` | 创建并初始化一个新的 Codex 会话，返回 Codex 实例和 thread_id |
| `Codex::submit` | `async fn submit(&self, op: Op) -> CodexResult<String>` | 提交操作到 Session 的处理队列 |
| `Codex::next_event` | `async fn next_event(&self) -> CodexResult<Event>` | 接收下一个事件 |
| `Codex::shutdown_and_wait` | `async fn shutdown_and_wait(&self) -> CodexResult<()>` | 发送 Shutdown 并等待会话循环终止 |
| `Codex::steer_input` | `async fn steer_input(...)` | 向当前活跃 Turn 注入额外用户输入 |
| `Session::new` | `async fn new(...) -> anyhow::Result<Arc<Self>>` | 创建 Session，初始化 rollout、shell 发现、MCP 连接管理器、网络代理、hooks 等 |
| `Session::build_per_turn_config` | `fn build_per_turn_config(...)` | 从 SessionConfiguration 构造每轮次的 Config |
| `Session::make_turn_context` | `fn make_turn_context(...)` | 构建 TurnContext |
| `Session::new_turn_with_sub_id` | `async fn new_turn_with_sub_id(...)` | 创建带设置更新的新 TurnContext |
| `Session::build_initial_context` | `async fn build_initial_context(...)` | 构建初始上下文（开发者指令、环境信息、App/Skills/Plugin 注入等） |
| `Session::record_context_updates_and_set_reference_context_item` | `async fn ...` | 记录上下文更新并设置引用基线 |
| `Session::request_command_approval` | `async fn request_command_approval(...)` | 发出 exec 审批请求并等待用户决策 |
| `Session::send_event` | `async fn send_event(...)` | 持久化事件到 rollout 并发送给客户端 |
| `submission_loop` | `async fn submission_loop(...)` | 会话主循环，处理所有 Op 提交（UserInput、Interrupt、Shutdown 等） |
| `run_turn` | `async fn run_turn(...)` | 执行一个完整 Turn：预压缩、上下文注入、技能/插件解析、模型采样循环、hook 执行 |
| `run_sampling_request` | `async fn run_sampling_request(...)` | 单次采样请求：工具构建、prompt 构造、重试/回退逻辑 |
| `try_run_sampling_request` | `async fn try_run_sampling_request(...)` | 实际的流式请求处理：接收 SSE/WebSocket 事件、处理 OutputItemDone、工具调用、plan 模式等 |
| `built_tools` | `async fn built_tools(...)` | 构建当前 Turn 的工具路由器（MCP 工具 + App 工具 + 可发现工具 + 动态工具） |
| `build_prompt` | `fn build_prompt(...)` | 从历史、工具、TurnContext 构建 Prompt |
| `handlers::*` | 各种 handler | interrupt、user_input_or_turn、exec_approval、thread_rollback、shutdown、review 等操作处理 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（协议定义）、`codex_api`（API 客户端）、`codex_features`（特性开关）、`codex_hooks`（hook 系统）、`codex_network_proxy`（网络代理）、`codex_otel`（遥测）、`codex_exec_server`（执行服务器）、`codex_analytics`（分析事件）、`codex_rmcp_client`（MCP 客户端）、`codex_sandboxing`（沙箱管理）、`tokio`、`futures`、`tracing` 等
- **被引用方**: `thread_manager.rs`（线程管理器创建 Codex 实例）、`tui_app.rs` / `app_server`（TUI 和 App Server 使用 Codex 接口）、测试文件

## 实现备注

1. **队列对模型**：Codex 通过 `async_channel` 实现 Submission/Event 双向通信，`submission_loop` 是核心事件循环。
2. **Turn 生命周期**：每个 Turn 由 `spawn_task` 启动，包含 TurnStarted -> 模型采样 -> 工具调用 -> TurnComplete/TurnAborted 完整生命周期。
3. **自动压缩**：当 token 使用量超过 `auto_compact_limit` 时触发 inline 或 remote 压缩。
4. **Plan 模式**：特殊的流式处理路径，将 `<proposed_plan>` 标签内容分离为 PlanDelta 事件。
5. **重试与回退**：WebSocket 流式请求失败时按 provider 配置的重试预算重试，超限后回退到 HTTP SSE。
6. **Guardian 审核**：通过 `GuardianReviewSessionManager` 支持子代理审核模式。
7. **Rollout 持久化**：所有事件和历史项通过 `RolloutRecorder` 持久化到 JSONL 文件，支持 resume/fork。
8. **Context Diff**：通过 `reference_context_item` 基线实现增量上下文更新，避免每轮重复注入完整上下文。
