# `lib.rs` — app-server crate 根模块与主运行循环

## 功能概述

本文件是 `codex-app-server` crate 的根模块，声明了所有子模块并导出公开 API。核心入口函数 `run_main` 和 `run_main_with_transport` 负责初始化完整的 app-server 运行时：解析 CLI 配置、加载云端配置需求、构建 Config、初始化 OpenTelemetry 和日志系统、启动传输层（stdio 或 WebSocket）、创建 `MessageProcessor`、以及运行主事件循环。主循环通过 `tokio::select!` 同时处理传输事件、线程创建事件和优雅重启信号。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `OutboundControlEvent` | 枚举 | 出站路由控制事件：`Opened`、`Closed`、`DisconnectAll` |
| `ShutdownState` | 结构体 | 优雅关闭状态机（首次信号 → 等待 Turn 完成 → 第二次信号强制关闭） |
| `LogFormat` | 枚举 | 日志格式：`Default` 或 `Json` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_main` | `async fn(arg0_paths, cli_overrides, loader_overrides, analytics) -> IoResult<()>` | stdio 模式的简化入口 |
| `run_main_with_transport` | `async fn(..., transport, session_source, auth) -> IoResult<()>` | 通用入口，支持 stdio/WebSocket |
| `shutdown_signal` | `async fn() -> IoResult<()>` | 等待 SIGTERM 或 Ctrl+C 信号 |
| `config_warning_from_error` | `fn(summary, err) -> ConfigWarningNotification` | 将配置加载错误转换为客户端警告通知 |
| `project_config_warning` | `fn(config) -> Option<ConfigWarningNotification>` | 检测被禁用的项目级配置文件并生成警告 |

## 依赖关系

- **引用的 crate**: `codex_core`（Config、AuthManager、ThreadManager）、`codex_cloud_requirements`、`codex_feedback`、`codex_state`、`tracing_subscriber`、`codex_otel`
- **被引用方**: `main.rs`（二进制入口）

## 实现备注

- 优雅重启：首次收到信号后进入 drain 模式，等待所有运行中的 assistant turn 完成；第二次信号强制关闭。
- stdio 模式在连接断开后立即退出；WebSocket 模式支持多连接。
- 日志格式通过 `LOG_FORMAT` 环境变量控制。
- 出站路由在独立 tokio 任务中运行，通过 `OutboundControlEvent` 与主循环协调连接状态。
