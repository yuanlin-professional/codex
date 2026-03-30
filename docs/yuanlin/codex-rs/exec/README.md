# exec

## 功能概述

`codex-exec` 是 Codex 项目的无头（headless）执行 crate，专为自动化和 CI/CD 场景设计。它以非交互方式运行 Codex Agent，接收单个 prompt，执行 Agent 循环直到完成，然后退出并报告结果。

核心特性：

- **非交互执行**：从命令行参数或 stdin 读取 prompt，无需终端 UI
- **双输出模式**：默认的人类可读格式（彩色 stderr 输出 + stdout 最终消息）和 `--json` 模式（stdout JSONL 事件流）
- **会话恢复**：`resume` 子命令支持按 ID、名称或 `--last` 恢复之前的会话
- **代码审查**：`review` 子命令支持审查未提交更改、基分支差异、特定 commit 或自定义指令
- **自动审批**：默认以 `AskForApproval::Never` 策略运行，适合 CI 环境中的全自动执行
- **最终消息导出**：`-o` / `--output-last-message` 将 Agent 最后的回复写入文件
- **输出 Schema**：`--output-schema` 指定 JSON Schema 约束 Agent 最终输出格式
- **OSS 模型支持**：`--oss` 配合 `--local-provider` 使用本地 LLM
- **OpenTelemetry 集成**：支持 `TRACEPARENT` 环境变量进行分布式追踪上下文传播
- **中断处理**：Ctrl+C 发送 `turn/interrupt` 请求，优雅终止当前 Agent 轮次

```mermaid
graph TB
    subgraph 输入
        CLI["命令行参数<br/>prompt / --image / --json"]
        Stdin["stdin<br/>管道输入"]
        Schema["--output-schema<br/>JSON Schema 文件"]
    end

    subgraph codex-exec
        MainFn["run_main()<br/>配置加载 / 认证 / OTel"]
        ExecSession["run_exec_session()<br/>会话执行循环"]
        EventLoop["事件循环<br/>server events + Ctrl+C"]
        EP["EventProcessor trait<br/>事件处理策略"]
        HumanOut["EventProcessorWithHumanOutput<br/>人类可读输出"]
        JsonOut["EventProcessorWithJsonOutput<br/>JSONL 输出"]
    end

    subgraph app-server (进程内)
        InProcess["InProcessAppServerClient<br/>进程内 app-server"]
        ThreadMgmt["thread/start | thread/resume"]
        TurnMgmt["turn/start | turn/interrupt"]
        ReviewMgmt["review/start"]
    end

    subgraph 输出
        Stderr["stderr<br/>配置摘要 / 进度 / 警告"]
        Stdout["stdout<br/>最终消息 / JSONL"]
        OutputFile["-o FILE<br/>最终消息文件"]
    end

    CLI --> MainFn
    Stdin --> MainFn
    Schema --> MainFn
    MainFn --> ExecSession
    ExecSession --> InProcess
    InProcess --> ThreadMgmt
    InProcess --> TurnMgmt
    InProcess --> ReviewMgmt
    ExecSession --> EventLoop
    EventLoop --> EP
    EP --> HumanOut
    EP --> JsonOut
    HumanOut --> Stderr
    HumanOut --> Stdout
    JsonOut --> Stdout
    HumanOut --> OutputFile
    JsonOut --> OutputFile
```

## 架构说明

exec 的执行流程分为三个阶段：

1. **初始化阶段**：加载配置、解析 CLI 参数、初始化 tracing/OTel、启动进程内 app-server 客户端。通过 `thread/start`（新会话）或 `thread/resume`（恢复会话）创建线程。

2. **执行阶段**：根据操作类型（用户轮次或代码审查）发送 `turn/start` 或 `review/start` 请求，然后进入事件循环。事件循环同时监听 app-server 事件（通知/请求）和 Ctrl+C 中断信号。

3. **输出阶段**：所有服务器请求（审批、elicitation 等）在 exec 模式下被自动拒绝，因为没有交互式 UI。事件处理器根据输出模式（人类可读或 JSONL）格式化并写出事件。最终 `TurnCompleted` 通知触发循环退出，输出 token 使用统计并以适当的退出码退出。

## 目录结构

| 文件/目录 | 说明 |
|---|---|
| `src/lib.rs` | 库入口，`run_main()` 函数、会话执行循环、事件处理、prompt 解析、resume 逻辑 |
| `src/main.rs` | 独立二进制入口，CLI 解析与 arg0 分发 |
| `src/cli.rs` | `Cli` 结构体定义（exec 专属命令行参数）、`Command` 枚举（Resume / Review）、`ReviewArgs` |
| `src/event_processor.rs` | `EventProcessor` trait 定义，事件处理策略接口 |
| `src/event_processor_with_human_output.rs` | 人类可读输出实现，彩色格式化的 stderr 进度显示 |
| `src/event_processor_with_jsonl_output.rs` | JSONL 输出实现，stdout 事件流 |
| `src/exec_events.rs` | exec 专属事件类型定义 |
| `tests/` | 集成测试目录 |

## 依赖关系

### 内部依赖

| Crate | 用途 |
|---|---|
| `codex-core` | 核心配置、认证、OTel 初始化、路径工具 |
| `codex-app-server-client` | 进程内 app-server 客户端 |
| `codex-app-server-protocol` | JSON-RPC 消息类型（ClientRequest、ServerNotification 等） |
| `codex-protocol` | 通用协议类型（SessionSource、SandboxPolicy 等） |
| `codex-arg0` | argv0 分发 |
| `codex-cloud-requirements` | 云端需求加载 |
| `codex-feedback` | 反馈收集 |
| `codex-git-utils` | Git 仓库检测 |
| `codex-otel` | OpenTelemetry 集成（traceparent 传播） |
| `codex-utils-cli` | CLI 配置覆盖 |
| `codex-utils-oss` | OSS 提供商工具 |
| `codex-utils-absolute-path` | 绝对路径工具 |

### 主要外部依赖

| Crate | 用途 |
|---|---|
| `clap` | 命令行参数解析 |
| `tokio` | 异步运行时（多线程、信号、进程） |
| `serde` / `serde_json` | JSON 序列化 |
| `tracing` / `tracing-subscriber` | 结构化日志 |
| `owo-colors` | 终端颜色输出 |
| `supports-color` | 终端颜色能力检测 |
| `uuid` | UUID 解析（会话 ID） |
| `ts-rs` | TypeScript 类型绑定生成 |
| `anyhow` | 错误处理 |

## 核心接口/API

### 公开函数

```rust
/// exec 主入口函数
pub async fn run_main(cli: Cli, arg0_paths: Arg0DispatchPaths) -> anyhow::Result<()>
```

### 公开类型

```rust
/// exec CLI 参数
pub struct Cli {
    pub command: Option<Command>,
    pub images: Vec<PathBuf>,
    pub model: Option<String>,
    pub oss: bool,
    pub full_auto: bool,
    pub dangerously_bypass_approvals_and_sandbox: bool,
    pub cwd: Option<PathBuf>,
    pub skip_git_repo_check: bool,
    pub ephemeral: bool,
    pub json: bool,
    pub last_message_file: Option<PathBuf>,
    pub output_schema: Option<PathBuf>,
    pub prompt: Option<String>,
    pub color: Color,
    pub config_overrides: CliConfigOverrides,
    // ...
}

/// exec 子命令
pub enum Command {
    Resume(ResumeArgs),
    Review(ReviewArgs),
}

/// 审查参数
pub struct ReviewArgs {
    pub uncommitted: bool,
    pub base: Option<String>,
    pub commit: Option<String>,
    pub commit_title: Option<String>,
    pub prompt: Option<String>,
}
```

### 内部核心类型

```rust
/// 事件处理策略 trait
trait EventProcessor {
    fn print_config_summary(&mut self, config, prompt, session_configured);
    fn process_server_notification(&mut self, notification) -> CodexStatus;
    fn process_warning(&mut self, message) -> CodexStatus;
    fn print_final_output(&mut self);
}

/// 执行状态
enum CodexStatus {
    Running,
    InitiateShutdown,
}

/// JSONL 事件处理器
pub struct EventProcessorWithJsonOutput { ... }

/// exec 事件类型（公开用于 TypeScript 绑定）
pub mod exec_events { ... }
```
