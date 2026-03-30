# `connectors_tests.rs` — 测试模块

## 测试覆盖范围

连接器（Connectors）管理逻辑，包括连接器合并、插件名称去重、可访问连接器缓存刷新、应用工具策略解析、全局/应用级/工具级启用与审批配置、需求约束应用、不允许连接器过滤、工具建议发现功能。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `merge_connectors_replaces_plugin_placeholder_name_with_accessible_name` | 合并连接器时用可访问名称替换插件占位符名称 |
| `accessible_connectors_from_mcp_tools_carries_plugin_display_names` | 从 MCP 工具获取的可访问连接器携带插件显示名称 |
| `refresh_accessible_connectors_cache_from_mcp_tools_writes_latest_installed_apps` | 从 MCP 工具刷新可访问连接器缓存写入最新已安装应用 |
| `merge_connectors_unions_and_dedupes_plugin_display_names` | 合并连接器时对插件显示名称进行联合去重 |
| `accessible_connectors_from_mcp_tools_preserves_description` | 从 MCP 工具获取的可访问连接器保留描述信息 |
| `app_tool_policy_uses_global_defaults_for_destructive_hints` | 应用工具策略使用全局默认的 destructive 提示 |
| `app_is_enabled_uses_default_for_unconfigured_apps` | 未配置应用使用默认启用状态 |
| `app_is_enabled_prefers_per_app_override_over_default` | 每应用覆盖优先于默认设置 |
| `requirements_disabled_connector_overrides_enabled_connector` | 需求禁用连接器覆盖已启用连接器 |
| `requirements_enabled_does_not_override_disabled_connector` | 需求启用不覆盖已禁用连接器 |
| `cloud_requirements_disable_connector_overrides_user_apps_config` | 云需求禁用连接器覆盖用户应用配置 |
| `cloud_requirements_disable_connector_applies_without_user_apps_table` | 无用户应用表时云需求禁用连接器仍生效 |
| `local_requirements_disable_connector_overrides_user_apps_config` | 本地需求禁用连接器覆盖用户应用配置 |
| `local_requirements_disable_connector_applies_without_user_apps_table` | 无用户应用表时本地需求禁用连接器仍生效 |
| `with_app_enabled_state_preserves_unrelated_disabled_connector` | 设置应用启用状态时保留不相关的已禁用连接器 |
| `app_tool_policy_honors_default_app_enabled_false` | 应用工具策略遵循默认应用启用为 false |
| `app_tool_policy_allows_per_app_enable_when_default_is_disabled` | 默认禁用时允许每应用单独启用 |
| `app_tool_policy_per_tool_enabled_true_overrides_app_level_disable_flags` | 工具级启用覆盖应用级禁用标志 |
| `app_tool_policy_default_tools_enabled_true_overrides_app_level_tool_hints` | 默认工具启用覆盖应用级工具提示 |
| `app_tool_policy_default_tools_enabled_false_overrides_app_level_tool_hints` | 默认工具禁用覆盖应用级工具提示 |
| `app_tool_policy_uses_default_tools_approval_mode` | 应用工具策略使用默认工具审批模式 |
| `app_tool_policy_matches_prefix_stripped_tool_name_for_tool_config` | 应用工具策略匹配去除前缀的工具名用于工具配置 |
| `filter_disallowed_connectors_allows_non_disallowed_connectors` | 过滤不允许连接器时允许正常连接器 |
| `filter_disallowed_connectors_filters_openai_prefix` | 过滤具有 openai 前缀的连接器 |
| `filter_disallowed_connectors_filters_disallowed_connector_ids` | 过滤不允许的连接器 ID |
| `first_party_chat_originator_filters_target_and_openai_prefixed_connectors` | 第一方聊天发起者过滤目标和 openai 前缀连接器 |
| `tool_suggest_connector_ids_include_configured_tool_suggest_discoverables` | 工具建议连接器 ID 包含配置的可发现项 |
| `filter_tool_suggest_discoverable_connectors_keeps_only_plugin_backed_uninstalled_apps` | 工具建议过滤仅保留插件支持的未安装应用 |
| `filter_tool_suggest_discoverable_connectors_excludes_accessible_apps_even_when_disabled` | 工具建议过滤排除可访问应用（即使已禁用） |
