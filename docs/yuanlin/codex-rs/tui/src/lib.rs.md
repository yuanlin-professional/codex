# `lib.rs` — TUI 库入口与应用启动流程

## 功能概述

本文件是 Codex TUI crate 的入口模块，负责模块声明、公开 API 导出和完整的应用启动流程编排。`run_main` 是最核心的入口函数，按顺序完成：配置加载与验证、日志初始化、OSS 提供者检测、OpenTelemetry 初始化、认证检查、引导界面、会话恢复/分叉、App Server 启动、以及最终进入 `App::run` 事件循环。

文件还包含大量辅助功能：远程 App Server 连接、WebSocket URL 规范化与安全验证、会话查找（按 ID/名称/最新）、CWD 解析与提示、配置重载、信任屏幕判断、登录状态检测、备用屏幕模式判断等。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppServerTarget` | 枚举 | App Server 目标：Embedded / Remote(websocket_url, auth_token) |
| `LoginStatus` | 枚举 | 登录状态：AuthMode(mode) / NotAuthenticated |
| `TerminalRestoreGuard` | 结构体 | 终端恢复守卫，确保退出时恢复终端状态（RAII） |
| `ResolveCwdOutcome` | 枚举 | CWD 解析结果：Continue(Option<PathBuf>) / Exit |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_main` | `async fn(cli, arg0_paths, ...) -> io::Result<AppExitInfo>` | TUI 主入口，完整的启动→运行→退出流程 |
| `normalize_remote_addr` | `fn(addr) -> Result<String>` | 规范化远程 App Server WebSocket URL |
| `resolve_cwd_for_resume_or_fork` | `async fn(tui, config, ...) -> Result<ResolveCwdOutcome>` | 恢复/分叉时解析工作目录 |
| `read_session_cwd` | `async fn(config, thread_id, path) -> Option<PathBuf>` | 读取会话的工作目录（优先 SQLite，回退 rollout 文件） |

## 依赖关系

- **引用的 crate**: 几乎所有内部模块、`codex_core`、`codex_protocol`、`codex_app_server_client`、`tracing_subscriber`、`color_eyre`、`url`
- **被引用方**: 顶层 `codex` 二进制入口

## 实现备注

- 模块声明使用条件编译控制 `voice`、`audio_device` 等平台相关模块。
- 远程 WebSocket URL 验证确保使用显式端口且 auth token 仅通过安全传输发送（wss 或 loopback ws）。
- 备用屏幕模式通过 `determine_alt_screen_mode` 实现自动检测，在 Zellij 中禁用以保留滚动缓冲区。
- 包含大量集成测试覆盖配置加载、App Server 启动、会话查找、CWD 解析等场景。
