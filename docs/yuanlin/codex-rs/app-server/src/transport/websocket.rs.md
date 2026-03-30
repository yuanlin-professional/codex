# `transport/websocket.rs` — WebSocket 传输层实现（axum 驱动）

## 功能概述

本模块基于 axum Web 框架实现 WebSocket 传输层。`start_websocket_acceptor` 绑定 TCP 端口，注册 `/readyz` 和 `/healthz` 健康检查端点以及 WebSocket 升级处理器。每个 WebSocket 连接分为入站和出站两个异步任务独立运行：入站任务转发 Text 消息到传输事件通道，出站任务从队列取消息序列化后发送。包含 Origin 头检测（拒绝浏览器跨域请求）和认证策略（在升级时调用 `authorize_upgrade`）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `WebSocketListenerState` | 结构体 | axum 共享状态：transport_event_tx、连接计数器、认证策略 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_websocket_acceptor` | `async fn(bind_address, tx, shutdown_token, auth_policy) -> IoResult<JoinHandle>` | 启动 WebSocket 监听器 |
| `websocket_upgrade_handler` | `async fn(ws, peer_addr, state, headers) -> impl IntoResponse` | 处理 WebSocket 升级请求 |
| `run_websocket_connection` | `async fn(connection_id, stream, tx)` | 运行单个连接的双向消息循环 |
| `run_websocket_outbound_loop` | `async fn(writer, rx, control_rx, disconnect_token)` | 出站消息发送循环 |
| `run_websocket_inbound_loop` | `async fn(reader, tx, writer_tx, control_tx, id, disconnect_token)` | 入站消息接收循环 |
| `reject_requests_with_origin_header` | `async fn(request, next) -> Result<Response>` | 中间件：拒绝携带 Origin 头的请求 |

## 依赖关系

- **引用的 crate**: `axum`（HTTP/WebSocket 框架）、`futures`（`SinkExt`、`StreamExt`）、`tokio_util`（`CancellationToken`）、`owo_colors`（启动横幅着色）
- **被引用方**: `lib.rs`（WebSocket 模式启动时调用）

## 实现备注

- 连接 ID 使用原子计数器从 1 开始递增分配（0 保留给 stdio）。
- Ping 消息自动回复 Pong；Binary 消息被忽略并警告。
- 出站循环通过 `disconnect_token` 支持优雅断开。
- 启动时在 stderr 打印彩色横幅，显示监听地址和端点 URL。
