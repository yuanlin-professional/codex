# `response_debug_context.rs` — API 响应调试信息提取

## 功能概述

此文件提供从 API 响应中提取调试信息的工具函数，包括请求 ID、Cloudflare Ray ID、认证错误和错误码等。它还提供了针对遥测的错误消息格式化函数，确保 HTTP 响应体中可能包含的敏感信息（如令牌）不会泄露到遥测数据中。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ResponseDebugContext` | 结构体 | 调试上下文，包含 request_id、cf_ray、auth_error、auth_error_code |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `extract_response_debug_context` | `(transport: &TransportError) -> ResponseDebugContext` | 从传输错误中提取调试上下文 |
| `extract_response_debug_context_from_api_error` | `(error: &ApiError) -> ResponseDebugContext` | 从 API 错误中提取调试上下文 |
| `telemetry_transport_error_message` | `(error: &TransportError) -> String` | 生成遥测安全的传输错误消息 |
| `telemetry_api_error_message` | `(error: &ApiError) -> String` | 生成遥测安全的 API 错误消息 |

## 依赖关系

- **引用的 crate**: `codex_api`（`TransportError`、`ApiError`）、`base64`、`serde_json`
- **被引用方**: 模型客户端在错误处理和遥测上报时使用

## 实现备注

- `x-error-json` 头使用 Base64 标准编码，解码后提取 `error.code` 字段
- 请求 ID 优先使用 `x-request-id`，回退到 `x-oai-request-id`
- 遥测消息中 HTTP 错误仅输出状态码（如 `"http 401"`），不包含响应体
- 非 HTTP 类型的错误（Network、Build、Stream）保留原始错误描述
