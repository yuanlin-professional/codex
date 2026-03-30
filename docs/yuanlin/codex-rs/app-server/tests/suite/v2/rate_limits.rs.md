# `rate_limits.rs` — 测试模块

## 测试覆盖范围
测试 `account/rateLimits/read` JSON-RPC 方法，验证需要认证、需要 ChatGPT 认证、
以及正常返回速率限制快照的行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `get_account_rate_limits_requires_auth` | 无认证时返回错误 |
| `get_account_rate_limits_requires_chatgpt_auth` | API Key 认证不足，需要 ChatGPT 认证 |
| `get_account_rate_limits_returns_snapshot` | ChatGPT 认证后返回速率限制快照 |
