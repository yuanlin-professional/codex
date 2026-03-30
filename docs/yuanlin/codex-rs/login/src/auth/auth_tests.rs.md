# `auth_tests.rs` — 测试模块

## 测试覆盖范围
验证认证管理器的核心逻辑：token 刷新、API key 登录、认证模式检测、plan type 映射、登录限制验证和刷新失败作用域控制。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `refresh_without_id_token` | 刷新 token 时保留原有 id_token |
| `login_with_api_key_overwrites_existing_auth_json` | API key 登录覆盖旧的 auth.json |
| `missing_auth_json_returns_none` | 无 auth.json 时返回 None |
| `pro_account_with_no_api_key_uses_chatgpt_auth` | Pro 账户无 API key 使用 ChatGPT 认证 |
| `loads_api_key_from_auth_json` | 从 auth.json 加载 API key |
| `logout_removes_auth_file` | 登出删除 auth 文件 |
| `plan_type_maps_known_plan` | 已知 plan type 正确映射 |
| `enforce_login_restrictions_logs_out_for_method_mismatch` | 登录方法不匹配时登出 |
| `enforce_login_restrictions_logs_out_for_workspace_mismatch` | workspace 不匹配时登出 |
| `refresh_failure_is_scoped_to_the_matching_auth_snapshot` | 刷新失败仅作用于匹配的 auth 快照 |
