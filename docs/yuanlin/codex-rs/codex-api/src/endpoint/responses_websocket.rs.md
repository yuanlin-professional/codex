# `responses_websocket.rs` — Responses WebSocket 客户端

## 功能概述

实现基于 WebSocket 传输的 Responses API 客户端。`ResponsesWebsocketClient` 负责建立连接（含 TLS、认证、permessage-deflate 压缩），`ResponsesWebsocketConnection` 负责发送请求和接收流式响应事件。内部通过 `WsStream` pump 任务实现全双工通信。支持连接级 idle timeout、服务端错误包装解析（含 429 和连接限制）和 turn state 传递。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ResponsesWebsocketConnection` | struct | WebSocket 连接句柄 |
| `ResponsesWebsocketClient<A>` | struct | WebSocket 客户端工厂 |
| `WsStream` | struct | WebSocket pump 任务封装 |
| `WrappedWebsocketErrorEvent` | struct | 服务端错误事件解析结构 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `connect` | `async fn(&self, headers, default_headers, turn_state, telemetry) -> Result<Connection>` | 建立 WebSocket 连接 |
| `stream_request` | `async fn(&self, request, reused) -> Result<ResponseStream>` | 发送请求并返回事件流 |
| `connect_websocket` | `async fn(url, headers, turn_state) -> Result<(WsStream, ...)>` | 底层连接建立 |

## 依赖关系

- **引用的 crate**: `tokio_tungstenite`, `tungstenite`, `codex_client`
- **被引用方**: `lib.rs`

## 实现备注

- 启用 permessage-deflate WebSocket 压缩扩展。
- 解析 `x-reasoning-included`、`x-models-etag`、`openai-model` 响应头。
- 连接限制错误（websocket_connection_limit_reached）映射为可重试错误。
- 包含详尽的单元测试覆盖错误解析和 header 合并逻辑。
