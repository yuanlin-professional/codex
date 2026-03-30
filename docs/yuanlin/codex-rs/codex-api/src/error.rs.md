# `error.rs` — API 错误类型

定义 `ApiError` 枚举，统一 codex-api 的错误类型，包括 Transport（HTTP 传输错误）、Api（状态码 + 消息）、Stream（流式错误）、ContextWindowExceeded、QuotaExceeded、UsageNotIncluded、Retryable（含可选延迟）、RateLimit、InvalidRequest 和 ServerOverloaded。实现从 `RateLimitError` 和 `TransportError` 的自动转换。
