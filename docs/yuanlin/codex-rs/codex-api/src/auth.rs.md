# `auth.rs` — 认证提供者接口和辅助函数

定义 `AuthProvider` trait（提供 bearer token 和 account ID）和 `add_auth_headers` / `add_auth_headers_to_header_map` 辅助函数，用于向 HTTP 请求注入 `Authorization: Bearer` 和 `ChatGPT-Account-ID` 请求头。
