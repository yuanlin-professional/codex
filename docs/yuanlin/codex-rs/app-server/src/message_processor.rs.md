# `message_processor.rs` — 入站消息路由与会话状态管理

## 功能概述

本模块实现了 `MessageProcessor`，作为 app-server 的中央消息路由器。它接收传输层传入的 JSON-RPC 请求/通知/响应/错误，将其路由到对应的处理逻辑。对于 JSON-RPC 请求，它先构建 tracing span，然后委托给内部的 `CodexMessageProcessor` 处理具体业务。它还管理每个连接的 `ConnectionSessionState`（初始化状态、实验性 API 标志、通知退出列表、客户端信息），以及外部认证刷新桥接（`ExternalAuthRefreshBridge`）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `MessageProcessor` | 结构体 | 中央消息处理器，持有 `CodexMessageProcessor`、`ConfigApi`、`FsApi`、`FsWatchManager` 等 |
| `ConnectionSessionState` | 结构体 | 每连接会话状态：初始化标志、实验性 API、客户端名称/版本 |
| `MessageProcessorArgs` | 结构体 | 构造 `MessageProcessor` 的参数包 |
| `ExternalAuthRefreshBridge` | 结构体 | 实现 `ExternalAuthRefresher` trait，将认证刷新请求桥接为客户端 JSON-RPC 请求 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(args) -> Self` | 构造消息处理器 |
| `process_request` | `async fn(&mut self, connection_id, request, transport, session)` | 处理 JSON-RPC 入站请求 |
| `process_client_request` | `async fn(&mut self, connection_id, request, session, outbound_initialized)` | 处理 v2 类型化客户端请求 |
| `process_response` | `async fn(&self, response)` | 处理客户端对服务端请求的响应 |
| `process_notification` | `async fn(&self, notification)` | 处理客户端通知 |
| `process_error` | `async fn(&self, error)` | 处理客户端错误响应 |
| `connection_closed` | `async fn(&self, connection_id)` | 连接断开时清理相关资源 |
| `send_initialize_notifications` | `async fn(&self)` | 发送初始化后的配置警告等通知 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`（JSON-RPC 消息类型）、`codex_core`（`AuthManager`、`ThreadManager`）、`codex_login`（`ExternalAuthRefresher`）
- **被引用方**: `lib.rs`（主循环）、`in_process.rs`（进程内运行时）

## 实现备注

- `ExternalAuthRefreshBridge` 通过 JSON-RPC `chatgptAuthTokens/refresh` 请求向客户端获取刷新后的令牌，超时 10 秒。
- `ConnectionSessionState` 的 `initialized` 标志控制出站通知的广播范围。
- 连接断开时会清理命令执行会话、文件监控、请求上下文等。
