# `headers.rs` — 请求头构建辅助函数

定义 `build_conversation_headers`（注入 session_id 头）、`subagent_header`（从 `SessionSource` 生成子代理标识）和通用的 `insert_header` 辅助函数，用于安全地添加 HTTP 请求头。
