# `role_tests.rs` — 测试模块

## 测试覆盖范围

覆盖 `role.rs` 的角色配置应用逻辑，包括：默认角色行为、未知角色错误处理、用户角色文件加载与验证、配置键保留与覆盖、Profile/Provider 选择逻辑、沙箱策略、技能配置、spawn 工具描述生成等。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `apply_role_defaults_to_default_and_leaves_config_unchanged` | 默认角色应用后配置应保持不变 |
| `apply_role_returns_error_for_unknown_role` | 未知角色名应返回错误 |
| `apply_explorer_role_sets_model_and_adds_session_flags_layer` | explorer 角色应设置模型并添加 SessionFlags 层（当前被 ignore） |
| `apply_role_returns_unavailable_for_missing_user_role_file` | 角色文件缺失时应返回 "不可用" 错误 |
| `apply_role_returns_unavailable_for_invalid_user_role_toml` | 角色文件格式无效时应返回 "不可用" 错误 |
| `apply_role_ignores_agent_metadata_fields_in_user_role_file` | 用户角色文件中的元数据字段（name/description/nickname_candidates）应被过滤 |
| `apply_role_preserves_unspecified_keys` | 角色未指定的配置键应保留原始值 |
| `apply_role_preserves_active_profile_and_model_provider` | 角色未指定 provider 时应保留当前 profile 和 provider |
| `apply_role_top_level_profile_settings_override_preserved_profile` | 角色顶层设置（model、reasoning 等）应覆盖保留的 profile 设置 |
| `apply_role_uses_role_profile_instead_of_current_profile` | 角色指定 profile 时应使用角色的 profile |
| `apply_role_uses_role_model_provider_instead_of_current_profile_provider` | 角色指定 model_provider 时应使用角色的 provider |
| `apply_role_uses_active_profile_model_provider_update` | 角色更新当前活跃 profile 的 provider 时应生效 |
| `apply_role_does_not_materialize_default_sandbox_workspace_write_fields` | 角色不应物化沙箱默认字段 |
| `apply_role_takes_precedence_over_existing_session_flags_for_same_key` | 角色层应覆盖已有 SessionFlags 层中的相同键 |
| `apply_role_skills_config_disables_skill_for_spawned_agent` | 角色中的技能配置应能禁用特定技能 |
| `spawn_tool_spec_build_deduplicates_user_defined_built_in_roles` | 工具描述生成应去重用户自定义与内置角色 |
| `spawn_tool_spec_lists_user_defined_roles_before_built_ins` | 用户自定义角色应排在内置角色前面 |
| `spawn_tool_spec_marks_role_locked_model_and_reasoning_effort` | 工具描述应标注锁定的模型和推理努力度 |
| `spawn_tool_spec_marks_role_locked_reasoning_effort_only` | 工具描述应标注仅锁定推理努力度的情况 |
| `built_in_config_file_contents_resolves_explorer_only` | 内置配置文件解析器对未知路径应返回 None |
