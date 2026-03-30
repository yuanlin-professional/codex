# `override_updates.rs` — 测试模块

## 测试覆盖范围
轮次上下文覆盖（OverrideTurnContext）在无后续用户轮次时的 rollout 记录行为，验证权限、环境和协作模式的更新不会被过早记录。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `override_turn_context_without_user_turn_does_not_record_permissions_update` | 无新用户轮次时，权限覆盖不记录到 rollout 中 |
| `override_turn_context_without_user_turn_does_not_record_environment_update` | 无新用户轮次时，环境上下文覆盖不记录到 rollout 中 |
| `override_turn_context_without_user_turn_does_not_record_collaboration_update` | 无新用户轮次时，协作模式覆盖不记录到 rollout 中 |
