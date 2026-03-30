# `transport/mod.rs` — 传输层抽象、出站路由与消息序列化

## 功能概述

本模块定义了 app-server 的传输层抽象和出站消息路由逻辑。`AppServerTransport` 枚举支持 `Stdio` 和 `WebSocket` 两种传输方式，可从 `--listen` URL 解析。核心的 `route_outgoing_envelope` 函数将出站消息路由到目标连接（定向或广播），处理连接断开、队列满时的背压策略（WebSocket 连接断开慢连接、stdio 连接等待）、通知退出过滤、以及实验性 API 字段剥离。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppServerTransport` | 枚举 | `Stdio` 或 `WebSocket { bind_address }` |
| `TransportEvent` | 枚举 | 传输事件：`ConnectionOpened`、`ConnectionClosed`、`IncomingMessage` |
| `ConnectionState` | 结构体 | 处理器侧的连接状态（session + 出站标志） |
| `OutboundConnectionState` | 结构体 | 出站路由侧的连接状态（writer + 认证标志 + 断开令牌） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `route_outgoing_envelope` | `async fn(connections, envelope)` | 将出站消息路由到目标连接 |
| `forward_incoming_message` | `async fn(transport_event_tx, writer, connection_id, payload) -> bool` | 将入站 JSON 文本解析并转发 |
| `enqueue_incoming_message` | `async fn(..., message) -> bool` | 入队入站消息，队列满时对请求返回 overload 错误 |
| `serialize_outgoing_message` | `fn(message) -> Option<String>` | 序列化出站消息为 JSON 字符串 |
| `filter_outgoing_message_for_connection` | `fn(state, message) -> OutgoingMessage` | 根据连接能力过滤消息字段 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`（`JSONRPCMessage`、`ServerRequest`）、`tokio::sync::mpsc`
- **被引用方**: `lib.rs`（主循环使用路由和传输事件）、`in_process.rs`

## 实现备注

- 有界通道容量为 128，平衡吞吐量和内存。
- WebSocket 连接在出站队列满时被主动断开（`can_disconnect` + `try_send`），stdio 连接使用 `send` 等待。
- `filter_outgoing_message_for_connection` 在未启用实验性 API 时剥离 `additional_permissions` 字段。
- 入站请求在传输队列满时返回 `-32001 Server overloaded` 错误，非请求消息则等待入队。
- 包含大量测试覆盖 URL 解析、overload 错误、慢连接断开、通知退出过滤等场景。
