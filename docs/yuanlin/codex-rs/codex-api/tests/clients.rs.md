# `clients.rs` — 测试模块

## 测试覆盖范围

验证 `ResponsesClient` 的 HTTP 请求构建、认证头注入、重试逻辑和 Azure 端点特殊处理。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `responses_client_uses_responses_path` | 请求 URL 以 /responses 结尾 |
| `streaming_client_adds_auth_headers` | 请求包含 Authorization、ChatGPT-Account-ID 和 Accept 头 |
| `streaming_client_retries_on_transport_error` | 首次请求失败后自动重试 |
| `azure_default_store_attaches_ids_and_headers` | Azure 端点自动附加 item ID 和 session/subagent 头 |
