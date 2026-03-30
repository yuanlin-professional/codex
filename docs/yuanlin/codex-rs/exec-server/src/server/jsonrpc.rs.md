# `jsonrpc.rs` — JSON-RPC 辅助工具函数

## 功能概述

提供服务端 JSON-RPC 消息构造工具函数，包括标准错误码工厂（`invalid_request`、`invalid_params`、`method_not_found`）以及从 `Result<Value, JSONRPCErrorError>` 构造 `JSONRPCMessage` 的 `response_message` 和 `invalid_request_message` 辅助函数。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `response_message` | `fn(request_id, Result<Value, Error>) -> JSONRPCMessage` | 成功/失败响应构造 |
| `invalid_request_message` | `fn(reason) -> JSONRPCMessage` | 固定 ID=-1 的请求格式错误响应 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`
- **被引用方**: `server/processor.rs`
