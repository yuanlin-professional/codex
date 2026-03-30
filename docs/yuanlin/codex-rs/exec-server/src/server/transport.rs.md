# `transport.rs` — WebSocket 传输层和监听器

## 功能概述

实现 exec-server 的 WebSocket 监听器。解析 `ws://IP:PORT` 格式的 listen URL，绑定 TCP 端口，接受 WebSocket 连接，并为每个连接启动独立的处理任务（`run_connection`）。启动时向 stdout 打印实际监听地址。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecServerListenUrlParseError` | enum | URL 解析错误：不支持的 scheme 或无效地址 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_listen_url` | `fn(&str) -> Result<SocketAddr>` | 解析 ws:// URL 为 SocketAddr |
| `run_transport` | `async fn(&str) -> Result<()>` | 启动 WebSocket 传输监听 |
| `run_websocket_listener` | `async fn(SocketAddr) -> Result<()>` | TCP 接受循环 |

## 依赖关系

- **引用的 crate**: `tokio::net`, `tokio_tungstenite`
- **被引用方**: `server.rs`（`run_main_with_listen_url`）
