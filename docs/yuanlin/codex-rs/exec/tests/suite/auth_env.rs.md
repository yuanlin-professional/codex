# `auth_env.rs` — 测试模块

## 测试覆盖范围

验证 exec 模式下 CODEX_API_KEY 环境变量的认证行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `exec_uses_codex_api_key_env_var` | 验证请求中包含 Authorization: Bearer dummy 头 |
