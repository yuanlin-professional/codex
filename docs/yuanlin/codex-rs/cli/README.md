# cli

## 功能概述

`codex-cli` 是 Codex 项目的命令行入口 crate，编译为 `codex` 可执行文件。它作为"多功能工具"（multitool）CLI，将所有子命令统一在一个二进制文件下，根据用户输入分发到对应的功能模块。

核心职责：

- **子命令分发**：将用户输入路由到 TUI（默认）、exec、review、app-server、MCP、login/logout、sandbox、cloud 等功能模块
- **统一配置传播**：根级别的 `-c key=value` 配置覆盖和 `--enable`/`--disable` 功能标志被正确传播到所有子命令
- **交互式会话管理**：`resume` 和 `fork` 子命令封装了复杂的会话恢复/分支逻辑，最终委托给 TUI
- **远程模式**：`--remote ws://host:port` 支持 TUI 连接远程 app-server
- **沙箱调试**：`sandbox` 子命令提供 macOS Seatbelt、Linux Landlock/bubblewrap、Windows 受限令牌沙箱的调试运行
- **认证管理**：`login`/`logout` 子命令管理 API key 和 ChatGPT 认证
- **工具链功能**：shell 补全生成、execpolicy 检查、TypeScript/JSON Schema 代码生成、功能标志管理
- **退出信息格式化**：统一处理 token 使用统计和会话恢复提示的格式化输出
- **更新执行**：当 TUI 检测到可用更新时，cli 负责实际执行更新命令

```mermaid
graph TB
    User["用户输入<br/>codex [OPTIONS] [COMMAND]"]

    subgraph codex-cli (MultitoolCli)
        Main["main.rs / cli_main()<br/>CLI 解析与分发"]
        ConfigFlags["-c key=value<br/>--enable / --disable"]
        Remote["--remote ws://...<br/>--remote-auth-token-env"]
    end

    subgraph 子命令分发
        Default["默认 (无子命令)<br/>→ 交互式 TUI"]
        ExecCmd["exec<br/>→ codex-exec"]
        ReviewCmd["review<br/>→ codex-exec (review)"]
        ResumeCmd["resume<br/>→ TUI (resume)"]
        ForkCmd["fork<br/>→ TUI (fork)"]
        AppServer["app-server<br/>→ codex-app-server"]
        McpServer["mcp-server<br/>→ codex-mcp-server"]
        McpCmd["mcp<br/>→ MCP 管理"]
        LoginCmd["login / logout<br/>→ codex-login"]
        SandboxCmd["sandbox<br/>→ 沙箱调试"]
        CompletionCmd["completion<br/>→ shell 补全"]
        CloudCmd["cloud<br/>→ codex-cloud-tasks"]
        ApplyCmd["apply<br/>→ codex-chatgpt apply"]
        FeaturesCmd["features<br/>→ 功能标志管理"]
        DebugCmd["debug<br/>→ 调试工具"]
    end

    subgraph 底层 crate
        TUI["codex-tui<br/>终端 UI"]
        Exec["codex-exec<br/>无头执行"]
        AppServerCrate["codex-app-server<br/>应用服务器"]
        McpServerCrate["codex-mcp-server<br/>MCP 服务器"]
        CloudTasks["codex-cloud-tasks<br/>云任务"]
        Chatgpt["codex-chatgpt<br/>ChatGPT apply"]
        Login["codex-login<br/>认证"]
        ExecPolicy["codex-execpolicy<br/>执行策略"]
        Sandbox["codex-sandboxing<br/>沙箱"]
    end

    User --> Main
    ConfigFlags --> Main
    Remote --> Main

    Main --> Default
    Main --> ExecCmd
    Main --> ReviewCmd
    Main --> ResumeCmd
    Main --> ForkCmd
    Main --> AppServer
    Main --> McpServer
    Main --> McpCmd
    Main --> LoginCmd
    Main --> SandboxCmd
    Main --> CompletionCmd
    Main --> CloudCmd
    Main --> ApplyCmd
    Main --> FeaturesCmd
    Main --> DebugCmd

    Default --> TUI
    ExecCmd --> Exec
    ReviewCmd --> Exec
    ResumeCmd --> TUI
    ForkCmd --> TUI
    AppServer --> AppServerCrate
    McpServer --> McpServerCrate
    CloudCmd --> CloudTasks
    ApplyCmd --> Chatgpt
    LoginCmd --> Login
    SandboxCmd --> Sandbox
```

## 架构说明

cli crate 的设计遵循"薄分发层"原则：它本身不包含业务逻辑，仅负责 CLI 参数解析与子命令路由。

### 参数解析层级

CLI 采用三层参数结构：

1. **根级别参数**（`MultitoolCli`）：`-c key=value` 配置覆盖、`--enable`/`--disable` 功能标志、`--remote` 远程模式
2. **默认交互参数**（`TuiCli`）：当无子命令时直接使用的 TUI 参数（`--model`、`--full-auto`、`--sandbox` 等）
3. **子命令参数**：每个子命令有自己的参数集

配置覆盖使用 `prepend_config_flags()` 函数合并，确保根级别覆盖的优先级低于子命令级别。

### resume/fork 处理

`resume` 和 `fork` 子命令有特殊的参数合并逻辑（`finalize_resume_interactive`/`finalize_fork_interactive`），它们将子命令参数合并到 TUI CLI 结构体中，然后委托给 `codex_tui::run_main()` 执行。这种设计使得 resume/fork 共享 TUI 的完整功能表面积。

### 远程模式验证

`--remote` 仅支持交互式 TUI 命令（默认、resume、fork），对所有其他子命令会被 `reject_remote_mode_for_subcommand()` 拒绝。

## 目录结构

| 文件/目录 | 说明 |
|---|---|
| `src/main.rs` | 二进制入口，`MultitoolCli` 结构体定义、`Subcommand` 枚举、`cli_main()` 分发逻辑 |
| `src/lib.rs` | 库入口，导出 `SeatbeltCommand`、`LandlockCommand`、`WindowsCommand`、`debug_sandbox`、`login` |
| `src/login.rs` | 登录相关函数（API key、ChatGPT、设备码认证） |
| `src/debug_sandbox.rs` | 沙箱调试命令实现 |
| `src/debug_sandbox/` | 沙箱调试子模块 |
| `src/mcp_cmd.rs` | MCP 管理子命令 |
| `src/app_cmd.rs` | macOS 桌面应用启动（仅 macOS） |
| `src/desktop_app/` | 桌面应用安装逻辑（仅 macOS） |
| `src/exit_status.rs` | 退出状态码定义 |
| `src/wsl_paths.rs` | WSL 路径规范化（非 Windows） |

## 依赖关系

### 内部依赖

| Crate | 用途 |
|---|---|
| `codex-tui` | 交互式终端 UI（默认子命令、resume、fork） |
| `codex-exec` | 非交互执行（exec、review 子命令） |
| `codex-app-server` | 应用服务器（app-server 子命令） |
| `codex-app-server-protocol` | 协议类型定义 |
| `codex-app-server-test-client` | app-server 调试客户端 |
| `codex-mcp-server` | MCP 服务器 |
| `codex-chatgpt` | ChatGPT apply 命令 |
| `codex-cloud-tasks` | 云任务管理 |
| `codex-core` | 核心配置与认证 |
| `codex-config` | 配置文件路径常量 |
| `codex-protocol` | 通用协议类型 |
| `codex-arg0` | argv0 分发 |
| `codex-execpolicy` | 执行策略检查 |
| `codex-features` | 功能标志定义与验证 |
| `codex-login` | 登录认证 |
| `codex-sandboxing` | 沙箱策略 |
| `codex-state` | 状态数据库（clear-memories） |
| `codex-terminal-detection` | 终端检测（dumb terminal 警告） |
| `codex-responses-api-proxy` | Responses API 代理 |
| `codex-rmcp-client` | RMCP 客户端 |
| `codex-stdio-to-uds` | stdio 到 UDS 中继 |
| `codex-utils-cli` | CLI 配置覆盖 |

### 主要外部依赖

| Crate | 用途 |
|---|---|
| `clap` / `clap_complete` | 命令行解析与 shell 补全生成 |
| `tokio` | 异步运行时 |
| `anyhow` | 错误处理 |
| `owo-colors` | 终端颜色 |
| `supports-color` | 颜色能力检测 |
| `serde_json` | JSON 处理 |
| `toml` | TOML 解析 |
| `tracing` / `tracing-appender` / `tracing-subscriber` | 日志 |
| `regex-lite` | 轻量正则 |
| `tempfile` | 临时文件 |
| `libc` | POSIX 系统调用 |

## 核心接口/API

### 公开类型（`codex-cli` 库）

```rust
/// macOS Seatbelt 沙箱命令参数
pub struct SeatbeltCommand {
    pub full_auto: bool,
    pub log_denials: bool,
    pub config_overrides: CliConfigOverrides,
    pub command: Vec<String>,
}

/// Linux Landlock 沙箱命令参数
pub struct LandlockCommand {
    pub full_auto: bool,
    pub config_overrides: CliConfigOverrides,
    pub command: Vec<String>,
}

/// Windows 受限令牌沙箱命令参数
pub struct WindowsCommand {
    pub full_auto: bool,
    pub config_overrides: CliConfigOverrides,
    pub command: Vec<String>,
}

/// 沙箱调试运行函数
pub mod debug_sandbox {
    pub async fn run_command_under_seatbelt(cmd, sandbox_exe) -> anyhow::Result<()>;
    pub async fn run_command_under_landlock(cmd, sandbox_exe) -> anyhow::Result<()>;
    pub async fn run_command_under_windows(cmd, sandbox_exe) -> anyhow::Result<()>;
}

/// 登录管理函数
pub mod login {
    pub fn read_api_key_from_stdin() -> String;
    pub async fn run_login_status(overrides: CliConfigOverrides);
    pub async fn run_login_with_api_key(overrides, api_key);
    pub async fn run_login_with_chatgpt(overrides);
    pub async fn run_login_with_device_code(overrides, issuer_url, client_id);
    pub async fn run_logout(overrides);
}
```

### 内部核心类型（`main.rs`）

```rust
/// 顶层多功能 CLI 结构体
struct MultitoolCli {
    config_overrides: CliConfigOverrides,
    feature_toggles: FeatureToggles,
    remote: InteractiveRemoteOptions,
    interactive: TuiCli,
    subcommand: Option<Subcommand>,
}

/// 子命令枚举（完整列表）
enum Subcommand {
    Exec(ExecCli),
    Review(ReviewArgs),
    Login(LoginCommand),
    Logout(LogoutCommand),
    Mcp(McpCli),
    McpServer,
    AppServer(AppServerCommand),
    App(AppCommand),          // macOS only
    Completion(CompletionCommand),
    Sandbox(SandboxArgs),
    Debug(DebugCommand),
    Execpolicy(ExecpolicyCommand),
    Apply(ApplyCommand),
    Resume(ResumeCommand),
    Fork(ForkCommand),
    Cloud(CloudTasksCli),
    ResponsesApiProxy(ResponsesApiProxyArgs),
    StdioToUds(StdioToUdsCommand),
    Features(FeaturesCli),
}

/// 功能标志开关
struct FeatureToggles {
    enable: Vec<String>,    // --enable FEATURE
    disable: Vec<String>,   // --disable FEATURE
}

/// 远程模式选项
struct InteractiveRemoteOptions {
    remote: Option<String>,              // --remote ws://host:port
    remote_auth_token_env: Option<String>, // --remote-auth-token-env ENV_VAR
}
```
