# `skills_list.rs` — 测试模块

## 测试覆盖范围
测试 `skills/list` JSON-RPC 方法，验证按 cwd 额外用户根目录查找技能、
拒绝相对路径、未知 cwd 忽略额外根、缓存机制和强制重载，以及技能变更通知。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `skills_list_includes_skills_from_per_cwd_extra_user_roots` | 包含 cwd 对应的额外用户根目录中的技能 |
| `skills_list_rejects_relative_extra_user_roots` | 拒绝相对路径的额外用户根 |
| `skills_list_ignores_per_cwd_extra_roots_for_unknown_cwd` | 未知 cwd 忽略额外根目录 |
| `skills_list_uses_cached_result_until_force_reload` | 使用缓存结果直到强制重载 |
| `skills_changed_notification_is_emitted_after_skill_change` | 技能变更后发送通知 |
