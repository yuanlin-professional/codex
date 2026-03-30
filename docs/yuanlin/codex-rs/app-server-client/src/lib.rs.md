# `lib.rs` — In-Process App-Server 客户端门面

## 功能概述

为 CLI 界面（TUI、exec）提供共享的进程内 app-server 客户端门面。封装 `codex_app_server::in_process` 并提供统一的异步 API，集中处理运行时启动、initialize 握手、类型化请求/通知分发、服务器请求解析/拒绝、带背压信号的事件消费和有界优雅关闭。通过 worker 任务桥接调用方的 mpsc 通道和底层 InProcessClientHandle，区分"必须送达"（transcript delta、item/turn 完成）和"尽力送达"（命令输出 delta）两级事件。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `InProcessAppServerClient` | struct | 进程内客户端门面，拥有 worker 任务和事件通道 |
| `InProcessClientStartArgs` | struct | 启动参数集合：config、arg0_paths、feedback 等 |
| `AppServerClient` | enum | InProcess 或 Remote 客户端的统一枚举 |
| `AppServerEvent` | enum | 统一事件类型：Notification、Request、Lagged、Disconnected |
| `TypedRequestError` | enum | Transport/Server/Deserialize 三层错误 |
| `ClientCommand` | enum (内部) | 发送到 worker 的命令：Request、Notify、Resolve、Reject、Shutdown |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start` | `async (args) -> IoResult<Self>` | 启动进程内运行时和 worker 任务 |
| `request_typed` | `async (&self, request) -> Result<T>` | 发送类型化请求并解码响应 |
| `next_event` | `async (&mut self) -> Option<InProcessServerEvent>` | 获取下一个事件 |
| `shutdown` | `async (self) -> IoResult<()>` | 有界优雅关闭 |
| `forward_in_process_event` | `async (...)` | 按照必须/尽力分级转发事件 |

## 依赖关系

- **引用的 crate**: `codex_app_server`、`codex_app_server_protocol`、`codex_core`、`codex_arg0`、`codex_feedback`
- **被引用方**: `exec/src/lib.rs`、TUI 模块
