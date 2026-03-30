# `util.rs` — 认证工具函数
提供 `try_parse_error_message` 函数，从 JSON 错误响应中提取 `error.message` 字段。若解析失败则回退到原始文本，空文本返回 "Unknown error"。
