# codex-rs/cli 架构文档

## 1. 概述

`codex-cli` 是 Codex 的命令行入口 crate，采用**薄分发层 (Thin Dispatch Layer)** 架构：使用 `clap` derive 宏解析命令行参数，统一管理配置覆盖链，将业务逻辑委托给 20+ 个后端 crate，处理平台特定逻辑和退出码。编译产物为单一二进制 `codex`。

规模：~25 源文件、~10,000 行代码。

---

## 2. 功能模块索引

| 模块标签 | 职责一句话 | 包含文件/目录 |
|----------|-----------|--------------|
| `[入口分发]` | 解析 CLI 参数并路由到对应子命令/后端 crate | `main.rs`, `lib.rs` |
| `[认证]` | ChatGPT OAuth、API Key、Device Code 等登录/注销流程 | `login.rs` |
| `[MCP管理]` | MCP 服务器的 list/get/add/remove/login/logout | `mcp_cmd.rs` |
| `[插件管理]` | 插件安装/列表/删除与市场源管理 | `plugin_cmd.rs`, `marketplace_cmd.rs` |
| `[诊断]` | 安装环境、配置、认证、网络、后台服务健康检查 | `doctor.rs`, `doctor/` |
| `[沙箱调试]` | 在 Seatbelt/Landlock/Windows 沙箱下运行命令 | `debug_sandbox.rs`, `debug_sandbox/` |
| `[远程控制]` | App Server 前台启动与远程控制连接管理 | `remote_control_cmd.rs` |
| `[桌面应用]` | 启动/安装 Codex Desktop（macOS/Windows） | `app_cmd.rs`, `desktop_app/` |
| `[状态恢复]` | SQLite 数据库损坏检测与安全修复 | `state_db_recovery.rs` |
| `[平台适配]` | WSL 路径转换、退出码处理 | `wsl_paths.rs`, `exit_status.rs` |

---

## 3. 逐目录逐文件说明

### 3.1 顶层文件 (src/*.rs)

顶层文件实现命令解析与分发，以及不归入子目录的独立命令模块。

**app_cmd.rs** `[桌面应用]` — 桌面应用启动命令（~25 行，仅 macOS/Windows）。
- `AppCommand` struct：workspace path + download URL override
- `run_app()` async fn：调用平台特定的 open-or-install 逻辑

**debug_sandbox.rs** `[沙箱调试]` — 沙箱调试主入口（~1090 行）。
- `run_command_under_seatbelt()` async fn（macOS）：解析 profile → 加载 Config → 构建 seatbelt args → spawn 子进程 → 可选 denial logging
- `run_command_under_landlock()` async fn（Linux）：解析 profile → 构建 landlock args → allow_network_for_proxy → spawn 子进程
- `run_command_under_windows()` async fn（Windows）：解析 profile → 构建 restricted token sandbox → spawn

**doctor.rs** `[诊断]` — 诊断系统主模块（~3900 行），实现只读的安装健康检查。
- `DoctorCommand` struct：`--json` 输出选项
- `run_doctor()` async fn：入口，构建并执行全部检查
- 检查分组：Environment（runtime/search/terminal/state）、Configuration（config/auth/mcp/sandbox）、Connectivity（network/websocket/reachability）、Background Server、Updates
- `DoctorReport` struct：包含 schema_version、overall_status、checks 列表
- `DoctorCheck` struct：id、category、status、summary、details、issues
- `DoctorIssue` struct：severity、cause、remedy
- `CheckStatus` enum：Ok / Warning / Fail
- 输出：`render_human_report()`（彩色分组）/ `redacted_json_report()`（机器可读）

**exit_status.rs** `[平台适配]` — 退出码处理（~23 行）。
- `handle_exit_status()` fn：Unix 下处理 signal 退出码（128 + signal）；Windows 下直接取 code

**lib.rs** `[入口分发]` — 公共接口，导出沙箱命令类型和 login 函数供外部集成测试使用。
- `SeatbeltCommand` / `LandlockCommand` / `WindowsCommand` struct：平台沙箱命令参数定义
- re-exports：`run_command_under_seatbelt`、`run_command_under_landlock`、`run_command_under_windows`、login 系列函数

**login.rs** `[认证]` — 认证流程实现（~470 行）。
- `run_login_with_chatgpt()` async fn：浏览器 OAuth 流程（启动本地 HTTP 服务器 → 打开浏览器 → 接收回调 token）
- `run_login_with_api_key()` async fn：从 stdin 读取 API key 并保存
- `run_login_with_access_token()` async fn：从 stdin 读取 access token 并保存
- `run_login_with_device_code()` async fn：设备码流程（显示 code + URL → 轮询授权状态）
- `run_login_with_device_code_fallback_to_browser()` async fn：设备码失败时降级到浏览器
- `run_login_status()` async fn：显示当前认证状态
- `run_logout()` async fn：清除凭证
- `read_api_key_from_stdin()` / `read_access_token_from_stdin()` fn
- `init_login_file_logging()` fn：独立的文件日志层（生成 `codex-login.log`）

**main.rs** `[入口分发]` — CLI 主入口，包含参数解析、子命令路由和 TUI 启动逻辑（~2100 行）。
- `MultitoolCli` struct：顶层 clap 结构，聚合 config_overrides + feature_toggles + interactive 选项 + subcommand
- `Subcommand` enum：所有子命令定义（Exec、Review、Login、Logout、Mcp、Plugin、McpServer、AppServer、RemoteControl、App、Completion、Update、Doctor、Sandbox、Debug、Execpolicy、Apply、Resume、Fork、Cloud、ResponsesApiProxy、StdioToUds、ExecServer、Features）
- `main()` fn：同步入口，调用 `arg0_dispatch_or_else()` 然后创建 tokio runtime
- `cli_main()` async fn：异步主逻辑，解析参数 → 配置合并 → match subcommand 分发
- `run_interactive_tui()` async fn：TUI 启动 + 终端检测 + DB 修复重试循环
- `handle_app_exit()` fn：处理 TUI 退出信息（token usage、resume hint、update action）
- `prepend_config_flags()` fn：将 `-c key=value` 和 feature toggles 合并到子命令的 config_overrides
- `run_update_command()` async fn：执行自动更新
- `FeatureToggles` struct：`--enable`/`--disable` feature flag 命令行参数
- `InteractiveRemoteOptions` struct：`--remote` 远程模式参数

**marketplace_cmd.rs** `[插件管理]` — 市场源管理子命令（~350 行）。
- `MarketplaceCli` struct / `MarketplaceSubcommand` enum：Add / List / Upgrade / Remove
- Add：支持本地路径和 Git URL（`--ref`、`--sparse`）
- Upgrade：刷新 Git 市场快照
- Remove：删除已配置的市场源

**mcp_cmd.rs** `[MCP管理]` — MCP 服务器管理命令实现（~940 行）。
- `McpCli` struct / `McpSubcommand` enum：list / get / add / remove / login / logout
- `McpCli::run()` async fn：主入口
- Add 逻辑：解析传输方式（Stdio `-- command args` / Streamable HTTP `--url`）→ 构建 `McpServerConfig` → 写入 config.toml
- Remove 逻辑：从 config.toml 删除指定条目
- Login/Logout：MCP OAuth 认证流程

**plugin_cmd.rs** `[插件管理]` — 插件管理命令实现（~520 行）。
- `PluginCli` struct / `PluginSubcommand` enum：Add / List / Marketplace / Remove
- `run_plugin_add()` async fn：解析 `PLUGIN@MARKETPLACE` → 查找市场源 → 安装插件
- `run_plugin_list()` async fn：列出已配置市场快照中的可用插件
- `run_plugin_remove()` async fn：删除已安装插件

**remote_control_cmd.rs** `[远程控制]` — App Server 远程控制命令（~700 行）。
- `RemoteControlCommand` struct / `RemoteControlSubcommand` enum：默认（前台启动） / Start / Stop
- 默认模式：前台启动 app-server → 创建 control socket → 等待连接 → 打印远程控制 URL
- Start：确保远程控制就绪
- Stop：停止远程控制服务

**state_db_recovery.rs** `[状态恢复]` — SQLite 数据库修复逻辑（~185 行）。
- `startup_error()` fn：从 IO 错误中提取 `LocalStateDbStartupError`
- `is_locked()` fn：判断是否为数据库锁定（非损坏）
- `confirm_repair()` fn：提示用户确认修复
- `repair_files()` async fn：备份 .db/.wal/.shm 文件并重建
- `print_repair_backups()` / `print_diagnostic_guidance()` / `print_locked_guidance()` fn

**wsl_paths.rs** `[平台适配]` — WSL 路径转换（~59 行，仅非 Windows）。
- `win_path_to_wsl()` fn：将 `C:\foo\bar` 转换为 `/mnt/c/foo/bar`
- `normalize_for_wsl()` fn：如在 WSL 下且为 Windows 路径则转换，否则原样返回

---

### 3.2 debug_sandbox/

沙箱调试子模块，提供平台特定的沙箱运行支持。

**debug_sandbox/pid_tracker.rs** `[沙箱调试]` — macOS 进程 PID 追踪，用于关联 denial 日志到正确的子进程树。

**debug_sandbox/seatbelt.rs** `[沙箱调试]` — macOS Seatbelt denial 日志捕获。
- `DenialLogger` struct：通过 `log stream` 捕获沙箱拒绝事件并在进程退出后打印

---

### 3.3 desktop_app/

桌面应用平台实现。

**desktop_app/mac.rs** `[桌面应用]` — macOS 桌面应用启动/安装逻辑。
- 检测已安装 app → 若无则下载安装器 → 打开 workspace

**desktop_app/mod.rs** `[桌面应用]` — 模块入口，条件编译选择平台实现。

**desktop_app/windows.rs** `[桌面应用]` — Windows 桌面应用启动/安装逻辑。

---

### 3.4 doctor/

诊断系统子模块，拆分各类检查逻辑。

**doctor/background.rs** `[诊断]` — 后台守护进程检查。
- `background_server_check()` fn：检查 app-server daemon 健康

**doctor/output.rs** `[诊断]` — 报告输出格式化。
- `render_human_report()` fn：彩色分组终端输出
- `redacted_json_report()` fn：脱敏 JSON 输出

**doctor/output/detail.rs** `[诊断]` — 检查详情渲染辅助。

**doctor/progress.rs** `[诊断]` — 检查进度显示（spinner/进度条）。

**doctor/runtime.rs** `[诊断]` — 运行时环境检查。
- `runtime_check()` fn：版本、平台、安装方式、二进制路径

**doctor/updates.rs** `[诊断]` — 更新检查。
- `updates_check()` fn：对比当前版本与最新发布

---

## 4. 模块间调用关系

```
入口分发 (main.rs)
├── 委托 → codex-tui (交互模式)
├── 委托 → codex-exec (非交互执行)
├── 委托 → codex-app-server (App Server)
├── 委托 → codex-mcp-server (MCP Server)
├── 委托 → codex-exec-server (Exec Server)
├── 委托 → codex-cloud-tasks (Cloud)
├── 调用 → 认证 (login.rs)
├── 调用 → MCP管理 (mcp_cmd.rs)
├── 调用 → 插件管理 (plugin_cmd.rs)
├── 调用 → 诊断 (doctor.rs)
├── 调用 → 沙箱调试 (debug_sandbox.rs)
├── 调用 → 远程控制 (remote_control_cmd.rs)
├── 调用 → 状态恢复 (state_db_recovery.rs)
└── 读取 → codex-core/config (配置构建)

认证 (login.rs)
├── 调用 → codex-login (OAuth/API key/device code)
└── 读取 → codex-core/config

MCP管理 (mcp_cmd.rs)
├── 调用 → codex-mcp (连接管理)
├── 调用 → codex-rmcp-client (OAuth)
└── 调用 → codex-core/config/edit (写入 config.toml)

插件管理 (plugin_cmd.rs)
└── 调用 → codex-core-plugins (PluginsManager)

诊断 (doctor.rs)
├── 调用 → codex-core/config (配置加载)
├── 调用 → codex-login (认证检查)
├── 调用 → codex-api (网络/WebSocket 探测)
└── 调用 → codex-install-context (安装信息)

沙箱调试 (debug_sandbox.rs)
├── 调用 → codex-sandboxing (构建沙箱命令)
└── 调用 → codex-core (config + exec_env)
```

---

## 5. 外部 crate 依赖

### 5.1 运行模式 crate（委托业务逻辑）

| Crate | 用途 |
|-------|------|
| `codex-tui` | 交互式 TUI 运行模式 |
| `codex-exec` | 非交互执行模式 |
| `codex-app-server` | App Server |
| `codex-app-server-daemon` | App Server 守护进程管理 |
| `codex-mcp-server` | MCP Server 模式 |
| `codex-exec-server` | Exec Server 模式 |
| `codex-cloud-tasks` | Cloud Tasks 模式 |
| `codex-chatgpt` | ChatGPT apply 命令 |
| `codex-responses-api-proxy` | Responses API 代理（内部） |

### 5.2 配置/状态 crate

| Crate | 用途 |
|-------|------|
| `codex-core` | 核心运行时（配置构建、exec_env） |
| `codex-config` | 配置基础类型 |
| `codex-protocol` | 协议类型 |
| `codex-features` | Feature flag |
| `codex-state` | SQLite 状态数据库路径/运行时 |
| `codex-utils-cli` | CLI 工具（CliConfigOverrides、resume_hint） |
| `codex-utils-absolute-path` | 绝对路径类型 |

### 5.3 认证/插件 crate

| Crate | 用途 |
|-------|------|
| `codex-login` | 认证流程实现 |
| `codex-api` | 模型 API 客户端 |
| `codex-model-provider` | 模型提供方 |
| `codex-models-manager` | 模型管理 |
| `codex-core-plugins` | 插件管理 |
| `codex-plugin` | 插件接口 |
| `codex-mcp` | MCP 连接管理 |
| `codex-rmcp-client` | RMCP/OAuth 客户端 |

### 5.4 沙箱/工具 crate

| Crate | 用途 |
|-------|------|
| `codex-sandboxing` | 沙箱命令构建 |
| `codex-windows-sandbox` | Windows 沙箱（仅 Windows） |
| `codex-arg0` | arg0 分发（多工具二进制） |
| `codex-rollout-trace` | Rollout 追踪/回放 |
| `codex-memories-write` | 记忆写入 |
| `codex-install-context` | 安装环境检测 |
| `codex-terminal-detection` | 终端检测 |
| `codex-execpolicy` | 执行策略检查命令 |

### 5.5 第三方 crate

| Crate | 用途 |
|-------|------|
| `clap` + `clap_complete` | CLI 解析、shell 补全生成 |
| `tokio` | 异步运行时 |
| `serde` + `serde_json` + `toml` | 序列化/反序列化 |
| `anyhow` | 错误传播 |
| `tracing` + `tracing-subscriber` + `tracing-appender` | 日志/追踪 |
| `owo-colors` + `supports-color` | 终端着色 |
| `crossterm` | 终端控制 |
| `libc` | Unix 系统调用 |
