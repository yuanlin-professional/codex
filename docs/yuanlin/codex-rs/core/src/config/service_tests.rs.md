# `service_tests.rs` -- 测试模块

## 测试覆盖范围
覆盖 `service.rs` 中 `ConfigService` 的读写 API、验证逻辑、层合并、版本冲突检测等功能。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `toml_value_to_item_handles_nested_config_tables` | 验证嵌套表的 toml::Value 到 toml_edit::Item 转换正确性 |
| `write_value_preserves_comments_and_order` | 验证写入值时保留原文件注释和键顺序 |
| `write_value_supports_nested_app_paths` | 验证支持嵌套的 app 配置路径写入 |
| `read_includes_origins_and_layers` | 验证 read API 返回正确的来源和层信息 |
| `write_value_succeeds_when_managed_preferences_expand_home_directory_paths` | (macOS) 验证 managed preferences 中 `~/` 路径扩展后写入成功 |
| `write_value_reports_override` | 验证写入值被 managed 层覆盖时正确报告 |
| `version_conflict_rejected` | 验证版本冲突被拒绝 |
| `write_value_defaults_to_user_config_path` | 验证省略 file_path 时默认写入用户配置 |
| `invalid_user_value_rejected_even_if_overridden_by_managed` | 验证无效值即使被 managed 覆盖也会被拒绝 |
| `reserved_builtin_provider_override_rejected` | 验证保留的内建 provider ID 不可覆盖 |
| `write_value_rejects_feature_requirement_conflict` | 验证特性需求冲突被拒绝 |
| `write_value_rejects_profile_feature_requirement_conflict` | 验证 profile 级特性需求冲突被拒绝 |
| `read_reports_managed_overrides_user_and_session_flags` | 验证 read 报告 managed 层覆盖用户和 session 层的情况 |
| `write_value_reports_managed_override` | 验证写入后报告 managed 覆盖 |
| `upsert_merges_tables_replace_overwrites` | 验证 Upsert 策略深度合并 vs Replace 策略完全覆盖 |
