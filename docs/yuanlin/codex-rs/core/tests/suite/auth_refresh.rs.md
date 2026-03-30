# `auth_refresh.rs` — 测试模块

## 测试覆盖范围

测试认证令牌(Token)刷新机制，包括成功刷新并更新存储、磁盘认证信息变更时的跳过刷新、账户不匹配时的错误处理、新鲜令牌的直接返回、过期令牌的自动刷新、永久性失败后的重试禁止，以及 401 未授权恢复流程。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `refresh_token_succeeds_updates_storage` | 刷新令牌成功后，验证存储和缓存均更新为新令牌 |
| `refresh_token_refreshes_when_auth_is_unchanged` | 认证信息未变更时正常执行刷新 |
| `refresh_token_skips_refresh_when_auth_changed` | 磁盘上的认证信息已变更时跳过刷新，直接使用磁盘令牌 |
| `refresh_token_errors_on_account_mismatch` | 磁盘令牌的账户 ID 与缓存不匹配时报错 |
| `returns_fresh_tokens_as_is` | 令牌仍在有效期内时直接返回，不触发刷新请求 |
| `refreshes_token_when_access_token_is_expired` | 访问令牌已过期时自动触发刷新 |
| `auth_reloads_disk_auth_when_cached_auth_is_stale` | 缓存认证过时时从磁盘重新加载 |
| `auth_reloads_disk_auth_without_calling_expired_refresh_token` | 从磁盘重新加载时不调用已过期的刷新端点 |
| `refresh_token_returns_permanent_error_for_expired_refresh_token` | 刷新令牌过期时返回永久性错误(Expired) |
| `refresh_token_does_not_retry_after_permanent_failure` | 永久性失败后不再重试刷新 |
| `refresh_token_reloads_changed_auth_after_permanent_failure` | 永久性失败后如磁盘认证已变更，重新加载而非重试 |
| `refresh_token_returns_transient_error_on_server_failure` | 服务端 500 错误时返回瞬时错误(Transient) |
| `unauthorized_recovery_reloads_then_refreshes_tokens` | 401 恢复流程：先重新加载磁盘认证，再刷新令牌 |
| `unauthorized_recovery_errors_on_account_mismatch` | 401 恢复流程中账户不匹配时报错 |
| `unauthorized_recovery_requires_chatgpt_auth` | 401 恢复流程仅支持 ChatGPT 认证模式，API Key 模式报错 |
