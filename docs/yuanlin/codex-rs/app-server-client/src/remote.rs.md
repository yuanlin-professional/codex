# `remote.rs` — 远程 WebSocket App-Server 客户端

## 功能概述

实现基于 WebSocket 的远程 app-server 客户端传输层。管理远程连接生命周期，包括 initialize/initialized 握手、JSON-RPC 请求/响应路由、服务器请求解析和通知流。支持可选的 Bearer token 认证（仅允许 wss:// 或 loopback ws://）。与进程内传输共享 `AppServerEvent` 和背压策略，使上层代码无需感知传输差异。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RemoteAppServerClient` | struct | 远程 WebSocket 客户端 |
| `RemoteAppServerConnectArgs` | struct | 连接参数：URL、auth_token、client info |
| `RemoteAppServerRequestHandle` | struct | 可克隆的请求句柄 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `connect` | `async (args) -> IoResult<Self>` | 建立 WebSocket 连接并完成 initialize 握手 |
| `websocket_url_supports_auth_token` | `(url) -> bool` | 验证 URL 是否安全承载 auth token |
| `initialize_remote_connection` | `async (...)` | 执行 initialize/initialized 握手 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`、`tokio_tungstenite`、`futures`
- **被引用方**: `app-server-client/src/lib.rs`
