# `methods.rs` — Realtime WebSocket 连接管理与事件处理

## 功能概述

实现 `RealtimeWebsocketClient`（连接建立）、`RealtimeWebsocketConnection`（消息收发门面）、`RealtimeWebsocketWriter`（发送端）和 `RealtimeWebsocketEvents`（接收端）。通过内部 `WsStream` pump 任务实现全双工非阻塞通信。支持音频帧发送、会话项创建、handoff 响应、会话更新和事件接收（含活跃转录追踪）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RealtimeWebsocketClient` | struct | 客户端工厂，负责 WebSocket 连接 |
| `RealtimeWebsocketConnection` | struct | 连接门面，包含 writer 和 events |
| `RealtimeWebsocketWriter` | struct | 可克隆的写入端（音频/消息/会话更新） |
| `RealtimeWebsocketEvents` | struct | 可克隆的事件接收端 |
| `WsStream` | struct | WebSocket pump 任务封装 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `RealtimeWebsocketClient::connect` | `async fn(&self, config, headers, default_headers) -> Result<Connection>` | 建立连接并发送 session.update |
| `send_audio_frame` | `async fn(&self, frame) -> Result<()>` | 发送音频帧 |
| `next_event` | `async fn(&self) -> Result<Option<RealtimeEvent>>` | 接收下一个解析后的事件 |
| `websocket_url_from_api_url` | `fn(api_url, params, model, parser, mode) -> Result<Url>` | API URL 到 WebSocket URL 转换 |

## 依赖关系

- **引用的 crate**: `tokio_tungstenite`, `url`, `codex_client`, `codex_utils_rustls_provider`
- **被引用方**: `lib.rs`

## 实现备注

- WsStream pump 任务处理 Ping/Pong 自动回复。
- 活跃转录在 V1 模式下随 handoff 事件传递。
- 包含大量单元测试覆盖事件解析、URL 构建和端到端通信。
