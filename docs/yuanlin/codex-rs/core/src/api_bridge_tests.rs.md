# `api_bridge_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖 API 桥接层的错误映射（map_api_error）和认证提供者（CoreAuthProvider）功能，包括服务器过载错误映射、使用限制（usage limit）错误的速率限制头解析、身份认证错误详情的提取（request-id、cf-ray、authorization error、x-error-json），以及认证头附加的状态报告。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `map_api_error_maps_server_overloaded` | 验证 ServerOverloaded API 错误映射为 CodexErr::ServerOverloaded |
| `map_api_error_maps_server_overloaded_from_503_body` | 验证 503 状态码且响应体含 server_is_overloaded 时映射为 ServerOverloaded |
| `map_api_error_maps_usage_limit_limit_name_header` | 验证使用限制错误正确提取 limit_name 头信息 |
| `map_api_error_does_not_fallback_limit_name_to_limit_id` | 验证缺少 limit_name 头时不回退使用 limit_id |
| `map_api_error_extracts_identity_auth_details_from_headers` | 验证 401 错误时正确提取 request-id、cf-ray、authorization error 和 x-error-json 中的错误码 |
| `core_auth_provider_reports_when_auth_header_will_attach` | 验证 CoreAuthProvider 持有 token 时报告认证头已附加及头名称 |
