# `api_bridge.rs` — API 错误映射与认证提供者

## 功能概述

此文件承担两个核心职责：一是将底层 `codex_api` 的错误类型（`ApiError`、`TransportError`）映射为 `codex_core` 使用的 `CodexErr` 类型，处理各种 HTTP 状态码和特殊错误场景；二是实现认证提供者（`CoreAuthProvider`），负责从环境变量、bearer token 或 CodexAuth 中获取认证令牌。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CoreAuthProvider` | 结构体 | 实现 `ApiAuthProvider` trait 的认证提供者，持有 token 和 account_id |
| `UsageErrorResponse` | 结构体（私有） | 429 响应中使用限制错误的反序列化结构 |
| `UsageErrorBody` | 结构体（私有） | 使用限制错误的详情体 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `map_api_error` | `(err: ApiError) -> CodexErr` | 将 API 错误映射为核心错误类型，处理上下文窗口超限、配额超限、速率限制等 |
| `auth_provider_from_auth` | `(auth, provider) -> Result<CoreAuthProvider>` | 从 API Key、bearer token 或 CodexAuth 构建认证提供者 |
| `extract_request_id` | `(headers) -> Option<String>` | 从响应头中提取 `x-request-id` 或 `x-oai-request-id` |
| `extract_x_error_json_code` | `(headers) -> Option<String>` | 解码 Base64 编码的 `x-error-json` 头并提取错误码 |

## 依赖关系

- **引用的 crate**: `codex_api`（API 错误和认证类型）、`codex_login`（`PlanType`）、`base64`、`http`、`serde`
- **被引用方**: 模型客户端使用 `map_api_error` 进行错误转换，会话初始化使用 `auth_provider_from_auth`

## 实现备注

- 429 错误时会区分 `usage_limit_reached`（使用限制）和 `usage_not_included`（未包含使用权限），并解析重置时间和速率限制信息
- 503 响应中 `server_is_overloaded` 和 `slow_down` 错误码会映射为 `ServerOverloaded`
- 400 错误中检测无效图片数据的特定错误消息并映射为 `InvalidImageRequest`
- 认证优先级：env_key > experimental_bearer_token > CodexAuth
- `x-error-json` 头使用 Base64 标准编码
