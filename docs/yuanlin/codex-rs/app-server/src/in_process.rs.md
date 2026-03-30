# `in_process.rs` — 进程内 app-server 运行时（无需 IPC 的嵌入式客户端）

## 功能概述

本模块实现了进程内（in-process）app-server 运行时，允许 CLI 等本地嵌入器在同一进程中使用 app-server 功能，避免跨进程通信。它复用了 `MessageProcessor` 和出站路由逻辑，但将 socket/stdio 传输替换为有界内存通道。`start` 函数自动完成 `initialize`/`initialized` 握手并返回可直接使用的 `InProcessClientHandle`。客户端通过 `request`/`notify`/`respond_to_server_request` 等方法与 app-server 交互，通过 `next_event` 消费服务端事件。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `InProcessStartArgs` | 结构体 | 启动参数（配置、CLI 覆盖、反馈通道等） |
| `InProcessClientHandle` | 结构体 | 客户端句柄，支持请求/通知/关闭操作 |
| `InProcessClientSender` | 结构体 | 可克隆的发送端，支持多处并发使用 |
| `InProcessServerEvent` | 枚举 | 服务端事件：`ServerRequest`、`ServerNotification`、`Lagged` |
| `InProcessClientMessage` | 枚举 | 客户端到运行时的内部消息类型 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start` | `async fn(args) -> IoResult<InProcessClientHandle>` | 启动运行时并完成初始化握手 |
| `InProcessClientHandle::request` | `async fn(&self, request) -> IoResult<Result>` | 发送类型化请求 |
| `InProcessClientHandle::notify` | `fn(&self, notification) -> IoResult<()>` | 发送通知 |
| `InProcessClientHandle::next_event` | `async fn(&mut self) -> Option<InProcessServerEvent>` | 接收下一个服务端事件 |
| `InProcessClientHandle::shutdown` | `async fn(self) -> IoResult<()>` | 请求关闭并等待运行时终止 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`（客户端请求/通知类型）、`codex_core`、`codex_exec_server`、`codex_feedback`
- **被引用方**: `codex-app-server-client` crate（高层 API 封装）

## 实现备注

- 使用 `try_send` 实现背压（可返回 `WouldBlock`），通知在饱和时可被丢弃。
- 服务端请求在无法入队时会被回复 overload/internal 错误，不会静默丢弃。
- `TurnCompleted` 通知使用 `send`（阻塞等待）而非 `try_send`，确保终端通知不丢失。
- 关闭超时为 5 秒，超时后会 abort 后台任务。
- 包含集成测试验证初始化握手、v2 请求处理、session source 传递、零容量通道钳位等。
