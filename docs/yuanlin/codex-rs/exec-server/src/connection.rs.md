# `connection.rs` — JSON-RPC 连接传输层

## 功能概述

实现 `JsonRpcConnection`，提供 WebSocket 和 stdio 两种传输后端的 JSON-RPC 消息收发能力。每个连接启动独立的 reader 和 writer 异步任务，通过 mpsc channel 解耦读写。支持文本/二进制 WebSocket 帧的解析以及错误和断连事件的传播。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `JsonRpcConnection` | struct | 封装出入消息通道和传输任务句柄 |
| `JsonRpcConnectionEvent` | enum | 连接事件：正常消息、格式错误、断连 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `from_websocket` | `fn(WebSocketStream, label) -> Self` | 从 WebSocket 流创建连接 |
| `from_stdio` | `fn(reader, writer, label) -> Self` | 从 stdio 流创建连接（仅测试） |
| `into_parts` | `fn(self) -> (Sender, Receiver, Vec<JoinHandle>)` | 拆分为通道和任务句柄 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`, `futures`, `tokio`, `tokio_tungstenite`
- **被引用方**: `client.rs`, `server/processor.rs`

## 实现备注

- `from_stdio` 用 `#[cfg(test)]` 标记，仅在测试中使用行分隔的 JSON-RPC。
- 信道容量为 128，防止慢消费者阻塞发送端。
