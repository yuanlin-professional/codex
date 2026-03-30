# `rpc.rs` — JSON-RPC 客户端/服务端框架

## 功能概述

实现通用的 JSON-RPC RPC 框架：`RpcClient` 用于客户端（带请求-响应关联和并发 pending 请求管理），`RpcRouter` 用于服务端（方法路由注册），`RpcNotificationSender` 用于发送通知。还提供标准 JSON-RPC 错误码的工厂函数。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RpcClient` | struct | JSON-RPC 客户端，支持异步 call 和 notify |
| `RpcRouter<S>` | struct | 方法路由表，注册请求和通知处理器 |
| `RpcNotificationSender` | struct | 服务端通知发送器 |
| `RpcCallError` | enum | RPC 调用错误：Closed/Json/Server |
| `RpcClientEvent` | enum | 客户端事件：Notification/Disconnected |
| `RpcServerOutboundMessage` | enum | 服务端出站消息：Response/Error/Notification |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `RpcClient::call` | `async fn<P, T>(&self, method, params) -> Result<T>` | 发送请求并等待响应 |
| `RpcClient::notify` | `async fn<P>(&self, method, params) -> Result<()>` | 发送通知 |
| `RpcRouter::request` | `fn(&mut self, method, handler)` | 注册请求处理器 |
| `invalid_request` / `method_not_found` / `invalid_params` / `internal_error` | `fn(String) -> JSONRPCErrorError` | 标准错误构造 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`, `tokio`, `serde`
- **被引用方**: `client.rs`, `local_process.rs`, `server/` 各模块

## 实现备注

- `RpcClient` 使用原子递增 `i64` 生成请求 ID。
- 乱序响应通过 `HashMap<RequestId, oneshot::Sender>` 正确关联。
- 连接断开时通过 `drain_pending` 向所有等待中的请求发送关闭错误。
