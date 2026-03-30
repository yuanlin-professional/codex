# `personality_migration.rs` — 测试模块

## 测试覆盖范围
个性迁移（Personality Migration）功能，验证在各种条件下（有无标记文件、有无会话、显式个性配置等）的迁移行为和幂等性。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `migration_marker_exists_no_sessions_no_change` | 迁移标记已存在且无会话时跳过迁移 |
| `no_marker_no_sessions_no_change` | 无标记无会话时跳过迁移并写入标记 |
| `no_marker_sessions_sets_personality` | 无标记有会话时应用迁移，设置个性为 Pragmatic |
| `no_marker_sessions_preserves_existing_config_fields` | 迁移时保留 config.toml 中的已有配置字段 |
| `no_marker_meta_only_rollout_is_treated_as_no_sessions` | 仅含元数据的 rollout 被视为无会话 |
| `no_marker_explicit_global_personality_skips_migration` | 已有全局个性配置时跳过迁移 |
| `no_marker_profile_personality_skips_migration` | 已有 profile 个性配置时跳过迁移 |
| `marker_short_circuits_invalid_profile_resolution` | 标记存在时短路跳过无效 profile 解析 |
| `invalid_selected_profile_returns_error_and_does_not_write_marker` | 无效 profile 引用返回错误且不写入标记 |
| `applied_migration_is_idempotent_on_second_run` | 已应用的迁移在二次运行时幂等 |
| `no_marker_archived_sessions_sets_personality` | 无标记但有归档会话时应用迁移 |
