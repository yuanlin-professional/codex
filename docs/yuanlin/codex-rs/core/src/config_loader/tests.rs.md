# `tests.rs` -- 测试模块

## 测试覆盖范围
覆盖 `config_loader/mod.rs` 中的配置层加载、合并、项目信任管理、需求约束验证等核心逻辑，以及 `requirements.toml` 解析和 exec policy 规则系统。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `cli_overrides_resolve_relative_paths_against_cwd` | 验证 CLI 覆盖中的相对路径相对于 cwd 解析 |
| `returns_config_error_for_invalid_user_config_toml` | 验证无效用户 config.toml 返回结构化配置错误 |
| `returns_config_error_for_invalid_managed_config_toml` | 验证无效 managed config 返回配置错误 |
| `returns_config_error_for_schema_error_in_user_config` | 验证 schema 级错误（如类型错误）返回配置错误 |
| `schema_error_points_to_feature_value` | 验证 schema 错误精确指向特性值位置 |
| `merges_managed_config_layer_on_top` | 验证 managed config 层覆盖用户层 |
| `returns_empty_when_all_layers_missing` | 验证所有配置文件缺失时返回空配置 |
| `managed_preferences_take_highest_precedence` | (macOS) 验证 MDM preferences 具有最高优先级 |
| `managed_preferences_expand_home_directory_in_workspace_write_roots` | (macOS) 验证 MDM 中 `~/` 路径扩展 |
| `managed_preferences_requirements_are_applied` | (macOS) 验证 MDM 需求约束被应用 |
| `managed_preferences_requirements_take_precedence` | (macOS) 验证 MDM 需求约束优先于 managed_config |
| `load_requirements_toml_produces_expected_constraints` | 验证 requirements.toml 解析为正确的约束 |
| `cloud_requirements_take_precedence_over_mdm_requirements` | (macOS) 验证云端需求优先于 MDM 需求 |
| `cloud_requirements_are_not_overwritten_by_system_requirements` | 验证云端需求不被系统需求覆盖 |
| `load_config_layers_includes_cloud_requirements` | 验证云端需求被包含在层栈中 |
| `load_config_layers_fails_when_cloud_requirements_loader_fails` | 验证云端需求加载失败时整体失败（fail closed） |
| `project_layers_prefer_closest_cwd` | 验证项目层按 cwd 最近优先排序 |
| `project_paths_resolve_relative_to_dot_codex_and_override_in_order` | 验证项目路径相对于 .codex 目录解析并按序覆盖 |
| `cli_override_model_instructions_file_sets_base_instructions` | 验证 CLI 覆盖 model_instructions_file 生效 |
| `project_layer_is_added_when_dot_codex_exists_without_config_toml` | 验证无 config.toml 的 .codex 目录仍生成空层条目 |
| `codex_home_is_not_loaded_as_project_layer_from_home_dir` | 验证 CODEX_HOME 不被当作项目层加载 |
| `codex_home_within_project_tree_is_not_double_loaded` | 验证项目树内的 CODEX_HOME 不被重复加载 |
| `project_layers_disabled_when_untrusted_or_unknown` | 验证不受信任或未知信任级别的项目层被禁用 |
| `cli_override_can_update_project_local_mcp_server_when_project_is_trusted` | 验证 CLI 覆盖可更新受信任项目的 MCP 服务器 |
| `cli_override_for_disabled_project_local_mcp_server_returns_invalid_transport` | 验证 CLI 覆盖不受信任项目的 MCP 服务器报错 |
| `invalid_project_config_ignored_when_untrusted_or_unknown` | 验证不受信任项目的无效配置被忽略 |
| `cli_overrides_with_relative_paths_do_not_break_trust_check` | 验证 CLI 相对路径覆盖不破坏信任检查 |
| `project_root_markers_supports_alternate_markers` | 验证自定义项目根标记（如 .hg）正常工作 |
| `requirements_exec_policy_tests::*` | 一组测试验证 requirements.toml 中 exec policy 规则的解析、转换和执行，包括前缀规则、any_of 展开、错误拒绝等 |
