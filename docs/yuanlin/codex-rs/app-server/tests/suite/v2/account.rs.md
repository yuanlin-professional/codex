# `account.rs` — 测试模块

## 测试覆盖范围
全面测试 v2 协议下的账户管理功能，包括登录（API Key、ChatGPT OAuth、设备码登录）、
登出、账户信息读取、外部认证令牌刷新、登录取消、强制登录方式限制等场景。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `logout_account_removes_auth_and_notifies` | 登出后删除 auth.json 并发送 account/updated 通知 |
| `set_auth_token_updates_account_and_notifies` | 设置 ChatGPT 令牌后更新账户并通知 |
| `account_read_refresh_token_is_noop_in_external_mode` | 外部模式下 refreshToken 参数为无操作 |
| `external_auth_refreshes_on_unauthorized` | 401 响应触发令牌刷新并重试请求 |
| `external_auth_refresh_error_fails_turn` | 客户端刷新返回错误时 turn 失败 |
| `external_auth_refresh_mismatched_workspace_fails_turn` | 刷新返回的 workspace 不匹配时 turn 失败 |
| `external_auth_refresh_invalid_access_token_fails_turn` | 刷新返回无效令牌时 turn 失败 |
| `login_account_api_key_succeeds_and_notifies` | API Key 登录成功并发送完成/更新通知 |
| `login_account_api_key_rejected_when_forced_chatgpt` | 强制 ChatGPT 时拒绝 API Key 登录 |
| `login_account_chatgpt_rejected_when_forced_api` | 强制 API 时拒绝 ChatGPT 登录 |
| `login_account_chatgpt_device_code_returns_error_when_disabled` | 设备码功能禁用时返回错误 |
| `login_account_chatgpt_device_code_succeeds_and_notifies` | 设备码登录完整流程成功 |
| `login_account_chatgpt_device_code_failure_notifies_without_account_update` | 设备码登录失败仅通知不更新账户 |
| `login_account_chatgpt_device_code_can_be_cancelled` | 设备码登录可被取消 |
| `login_account_chatgpt_start_can_be_cancelled` | ChatGPT OAuth 登录可被取消 |
| `set_auth_token_cancels_active_chatgpt_login` | 设置外部令牌自动取消进行中的登录 |
| `login_account_chatgpt_includes_forced_workspace_query_param` | 强制 workspace 参数出现在 auth URL 中 |
| `get_account_no_auth` | 无认证时 account/read 返回空 |
| `get_account_with_api_key` | API Key 认证后返回 ApiKey 账户类型 |
| `get_account_when_auth_not_required` | 认证非必需时返回空账户 |
| `get_account_with_chatgpt` | ChatGPT 认证后返回邮箱和计划类型 |
| `get_account_with_chatgpt_missing_plan_claim_returns_unknown` | 缺少计划声明时返回 Unknown 类型 |
