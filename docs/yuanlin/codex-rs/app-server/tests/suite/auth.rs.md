# `auth.rs` — 测试模块

## 测试覆盖范围
测试 app-server 的认证状态查询（getAuthStatus）和登录限制功能，涵盖无认证、API Key 认证、
ChatGPT 认证、令牌刷新成功/失败、主动刷新失败恢复，以及强制登录方式下的方法拒绝等场景。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `get_auth_status_no_auth` | 无认证时返回空的认证状态 |
| `get_auth_status_with_api_key` | API Key 登录后返回正确的认证方法和令牌 |
| `get_auth_status_with_api_key_when_auth_not_required` | 自定义提供者不要求 OpenAI 认证时，API Key 不显示 |
| `get_auth_status_with_api_key_no_include_token` | include_token 未设置时不返回令牌内容 |
| `get_auth_status_with_api_key_refresh_requested` | 请求令牌刷新时返回完整状态 |
| `get_auth_status_omits_token_after_permanent_refresh_failure` | 永久刷新失败后令牌被省略 |
| `get_auth_status_omits_token_after_proactive_refresh_failure` | 主动刷新失败后令牌被省略 |
| `get_auth_status_returns_token_after_proactive_refresh_recovery` | 主动刷新失败后重写认证可恢复 |
| `login_api_key_rejected_when_forced_chatgpt` | 强制 ChatGPT 登录时拒绝 API Key |
