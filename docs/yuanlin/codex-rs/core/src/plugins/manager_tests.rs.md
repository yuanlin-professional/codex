# `manager_tests.rs` -- 测试模块

## 测试覆盖范围
全面测试 `manager.rs` 中 `PluginsManager` 的核心功能，包括插件加载、安装、卸载、市场列表、远程同步、缓存刷新等场景。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `load_plugins_loads_default_skills_and_mcp_servers` | 验证从默认路径加载技能、MCP 服务器和应用连接器 |
| `load_plugins_resolves_disabled_skill_names_against_loaded_plugin_skills` | 验证根据配置禁用特定技能 |
| `load_plugins_ignores_unknown_disabled_skill_names` | 验证未知的技能名称不影响插件加载 |
| `plugin_telemetry_metadata_uses_default_mcp_config_path` | 验证遥测元数据使用默认 MCP 配置路径 |
| `capability_summary_sanitizes_plugin_descriptions_to_one_line` | 验证插件描述中的换行和多余空格被清理 |
| `capability_summary_truncates_overlong_plugin_descriptions` | 验证过长的插件描述被截断到 1024 字符 |
| `load_plugins_uses_manifest_configured_component_paths` | 验证使用清单中配置的自定义组件路径（skills/mcp/apps） |
| `load_plugins_ignores_manifest_component_paths_without_dot_slash` | 验证不以 `./` 开头的路径被忽略 |
| `load_plugins_preserves_disabled_plugins_without_effective_contributions` | 验证禁用的插件仍保留在列表中但不贡献有效资源 |
| `effective_apps_dedupes_connector_ids_across_plugins` | 验证跨插件的应用连接器 ID 去重 |
| `capability_index_filters_inactive_and_zero_capability_plugins` | 验证能力摘要过滤掉禁用和无能力的插件 |
| `load_plugins_returns_empty_when_feature_disabled` | 验证插件功能禁用时返回空结果 |
| `load_plugins_rejects_invalid_plugin_keys` | 验证无效的插件键（缺少 @marketplace）报错 |
| `install_plugin_updates_config_with_relative_path_and_plugin_key` | 验证安装插件后正确更新 config.toml |
| `uninstall_plugin_removes_cache_and_config_entry` | 验证卸载插件移除缓存目录和配置项 |
| `list_marketplaces_includes_enabled_state` | 验证市场列表包含插件的安装和启用状态 |
| `list_marketplaces_returns_empty_when_feature_disabled` | 验证功能禁用时市场列表为空 |
| `list_marketplaces_excludes_plugins_with_explicit_empty_products` | 验证显式空产品列表的插件被排除 |
| `read_plugin_for_config_returns_plugins_disabled_when_feature_disabled` | 验证功能禁用时读取插件返回 PluginsDisabled 错误 |
| `read_plugin_for_config_uses_user_layer_skill_settings_only` | 验证插件读取仅使用用户层配置，忽略项目层配置 |
| `sync_plugins_from_remote_returns_default_when_feature_disabled` | 验证功能禁用时远程同步返回默认结果 |
| `list_marketplaces_includes_curated_repo_marketplace` | 验证精选仓库市场自动包含在列表中 |
| `list_marketplaces_uses_first_duplicate_plugin_entry` | 验证重复插件使用第一个市场中的条目 |
| `list_marketplaces_marks_configured_plugin_uninstalled_when_cache_is_missing` | 验证缓存缺失时标记插件为未安装 |
| `sync_plugins_from_remote_reconciles_cache_and_config` | 验证远程同步正确协调缓存和配置（安装/启用/卸载） |
| `sync_plugins_from_remote_additive_only_keeps_existing_plugins` | 验证 additive_only 模式不移除现有插件 |
| `sync_plugins_from_remote_ignores_unknown_remote_plugins` | 验证远程中不存在于本地的插件被跳过 |
| `sync_plugins_from_remote_keeps_existing_plugins_when_install_fails` | 验证安装失败时保留现有插件 |
| `sync_plugins_from_remote_uses_first_duplicate_local_plugin_entry` | 验证同步时使用第一个重复本地条目 |
| `featured_plugin_ids_for_config_uses_restriction_product_query_param` | 验证推荐插件接口使用正确的产品查询参数 |
| `featured_plugin_ids_for_config_defaults_query_param_to_codex` | 验证无产品限制时默认使用 codex 参数 |
| `refresh_curated_plugin_cache_replaces_existing_local_version_with_sha` | 验证缓存刷新用 SHA 版本替换 local 版本 |
| `refresh_curated_plugin_cache_reinstalls_missing_configured_plugin_with_current_sha` | 验证刷新时重新安装缺失的已配置插件 |
| `configured_curated_plugin_ids_from_codex_home_reads_latest_user_config` | 验证从最新用户配置读取精选插件 ID |
| `refresh_curated_plugin_cache_returns_false_when_configured_plugins_are_current` | 验证已是最新版本时返回 false |
| `load_plugins_ignores_project_config_files` | 验证插件加载忽略项目级配置文件 |
