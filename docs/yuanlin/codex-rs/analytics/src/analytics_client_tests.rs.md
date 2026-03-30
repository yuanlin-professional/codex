# `analytics_client_tests.rs` — 测试模块

## 测试覆盖范围
验证分析事件的序列化格式和去重逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `normalize_path_for_skill_id_repo_scoped_uses_relative_path` | 仓库范围技能使用相对路径 |
| `normalize_path_for_skill_id_user_scoped_uses_absolute_path` | 用户范围技能使用绝对路径 |
| `app_mentioned_event_serializes_expected_shape` | 应用提及事件序列化格式正确 |
| `app_used_event_serializes_expected_shape` | 应用使用事件序列化格式正确 |
| `app_used_dedupe_is_keyed_by_turn_and_connector` | 应用使用事件按 turn+connector 去重 |
| `plugin_used_event_serializes_expected_shape` | 插件使用事件序列化格式正确 |
| `plugin_management_event_serializes_expected_shape` | 插件管理事件序列化格式正确 |
| `plugin_used_dedupe_is_keyed_by_turn_and_plugin` | 插件使用事件按 turn+plugin 去重 |
