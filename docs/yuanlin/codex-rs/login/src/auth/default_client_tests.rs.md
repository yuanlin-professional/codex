# `default_client_tests.rs` — 测试模块

## 测试覆盖范围
验证默认 HTTP 客户端的 User-Agent 格式、originator header 设置、residency header 注入和非法后缀消毒。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `test_get_codex_user_agent` | User-Agent 包含正确的 originator 前缀 |
| `is_first_party_originator_matches_known_values` | 第一方 originator 识别 |
| `test_create_client_sets_default_headers` | 客户端请求包含 originator/UA/residency headers |
| `test_invalid_suffix_is_sanitized` | 非法字符后缀被消毒 |
| `test_macos` | macOS 上 User-Agent 格式正确 |
