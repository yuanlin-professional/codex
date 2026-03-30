# `manager_tests.rs` — 测试模块

## 测试覆盖范围
该文件测试 `SkillsManager` 的核心行为，包括缓存复用、技能禁用规则的应用、内置技能过滤、会话标志覆盖用户配置、以及额外根路径加载等场景。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `new_with_disabled_bundled_skills_removes_stale_cached_system_skills` | 禁用内置技能时清理过期缓存的系统技能目录 |
| `skills_for_config_reuses_cache_for_same_effective_config` | 配置不变时复用配置感知缓存 |
| `skills_for_config_disables_plugin_skills_by_name` | 通过名称选择器禁用插件技能 |
| `skills_for_cwd_reuses_cached_entry_even_when_entry_has_extra_roots` | cwd 缓存条目含额外根时仍被复用 |
| `skills_for_config_excludes_bundled_skills_when_disabled_in_config` | 配置禁用内置技能时排除 System 作用域技能 |
| `skills_for_cwd_with_extra_roots_only_refreshes_on_force_reload` | 仅强制重载时刷新额外根路径的技能 |
| `normalize_extra_user_roots_is_stable_for_equivalent_inputs` | 额外根路径规范化结果对等价输入保持稳定 |
| `disabled_paths_for_skills_allows_session_flags_to_override_user_layer` | 会话标志可覆盖用户层禁用 |
| `disabled_paths_for_skills_allows_session_flags_to_disable_user_enabled_skill` | 会话标志可禁用用户层已启用的技能 |
| `disabled_paths_for_skills_disables_matching_name_selectors` | 名称选择器正确禁用匹配技能 |
| `disabled_paths_for_skills_allows_name_selector_to_override_path_selector` | 名称选择器可覆盖路径选择器 |
| `skills_for_config_ignores_cwd_cache_when_session_flags_reenable_skill` | 会话标志重新启用技能时不使用 cwd 缓存 |
