# `outgoing_message.rs` — 出站消息发送器与请求回调管理

## 功能概述

本模块实现了 `OutgoingMessageSender`，是 app-server 向客户端发送所有出站消息的核心组件。它管理服务端请求的生命周期（发送请求 → 注册 oneshot 回调 → 等待客户端响应/错误 → 触发回调）、请求上下文跟踪（tracing span 和 trace context）、以及各类消息的路由（响应路由到特定连接、通知广播到所有/指定连接）。`ThreadScopedOutgoingMessageSender` 是线程级的封装，将消息限定到订阅该线程的连接集合。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `OutgoingMessageSender` | 结构体 | 核心发送器，管理请求回调和请求上下文 |
| `ThreadScopedOutgoingMessageSender` | 结构体 | 线程级封装，限定目标连接集合 |
| `ConnectionId` | 结构体 | 传输连接的稳定标识 |
| `ConnectionRequestId` | 结构体 | 连接 ID + 请求 ID 的复合键 |
| `RequestContext` | 结构体 | 请求的追踪上下文（span + trace） |
| `OutgoingEnvelope` | 枚举 | `ToConnection`（定向）或 `Broadcast`（广播） |
| `OutgoingMessage` | 枚举 | `Request`、`AppServerNotification`、`Response`、`Error` |
| `QueuedOutgoingMessage` | 结构体 | 带可选写完成通知的排队消息 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `send_request` | `async fn(&self, payload) -> (RequestId, Receiver)` | 发送服务端请求并返回回调接收器 |
| `send_response` | `async fn(&self, request_id, response)` | 发送响应到指定连接 |
| `send_error` | `async fn(&self, request_id, error)` | 发送错误到指定连接 |
| `send_server_notification` | `async fn(&self, notification)` | 广播服务端通知 |
| `notify_client_response` | `async fn(&self, id, result)` | 触发客户端响应回调 |
| `notify_client_error` | `async fn(&self, id, error)` | 触发客户端错误回调 |
| `cancel_requests_for_thread` | `async fn(&self, thread_id, error)` | 取消指定线程的所有待处理请求 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`（`ServerRequest`、`ServerNotification`）、`codex_otel`（span trace context）、`codex_protocol`
- **被引用方**: 几乎所有模块（`message_processor`、`bespoke_event_handling`、`codex_message_processor`、`in_process` 等）

## 实现备注

- 服务端请求 ID 使用原子计数器自增分配。
- `send_server_notification_to_connection_and_wait` 提供写完成等待功能，确保通知实际写入传输层后才返回。
- 响应/错误发送时会清理关联的 `RequestContext`，并在请求 span 的上下文中执行发送操作。
- 包含丰富的单元测试覆盖序列化、路由、回调触发、上下文清理等场景。
