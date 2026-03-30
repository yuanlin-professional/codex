# `app.rs` — TUI 应用主循环与核心调度

## 功能概述

本文件是 Codex TUI 的核心模块，定义了 `App` 结构体和主事件循环。`App` 负责协调所有 TUI 子系统：管理 App Server 会话、处理用户输入事件（键盘、粘贴）、处理来自 App Server 的通知和请求、分发 `AppEvent` 内部事件、管理 overlay/popup 生命周期、处理线程切换/恢复/分叉、配置持久化等。

文件体量非常大（超过 10000 行），包含了 TUI 运行期间几乎所有的协调逻辑，包括但不限于：模型切换、权限管理、回溯、差异渲染、外部编辑器集成、文件搜索、插件管理、多 Agent 支持、实时音频等。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `App` | 结构体 | TUI 应用主体，持有所有运行时状态 |
| `AppExitInfo` | 结构体 | 应用退出信息：token 使用量、线程 ID/名称、更新操作、退出原因 |
| `ExitReason` | 枚举 | 退出原因：UserRequested / Fatal / SessionFatalError |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `App::run` | `async fn(tui, session, config, ...) -> Result<AppExitInfo>` | 应用主入口，运行完整的事件循环 |

## 依赖关系

- **引用的 crate**: 几乎所有 TUI 内部模块、`codex_core`、`codex_protocol`、`codex_app_server_protocol`、`codex_app_server_client`
- **被引用方**: `lib.rs` 中的 `run_ratatui_app` 调用 `App::run`

## 实现备注

由于文件规模巨大，许多功能（回溯、多 Agent、overlay 管理等）通过 `impl App` 在其他文件中扩展（如 `app_backtrack.rs`）。主事件循环使用 `tokio::select!` 同时监听终端事件、App Server 事件和内部 AppEvent 通道。
