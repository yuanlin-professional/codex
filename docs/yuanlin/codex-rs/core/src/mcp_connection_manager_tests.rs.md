# `mcp_connection_manager_tests.rs` — 测试模块

## 测试覆盖范围

MCP 连接管理器（McpConnectionManager），包括 elicitation 策略检查、工具名称限定与去重、工具过滤器、Codex Apps 工具缓存（写入/读取/用户隔离/版本校验/不允许连接器过滤）、启动快照加载、工具列表行为、elicitation 能力检测、MCP 初始化错误显示、传输来源提取。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `elicitation_granular_policy_defaults_to_prompting` | elicitation 细粒度策略默认为提示 |
| `elicitation_granular_policy_respects_never_and_config` | elicitation 细粒度策略遵循 Never 和配置 |
| `test_qualify_tools_short_non_duplicated_names` | 短且不重复的工具名限定正确 |
| `test_qualify_tools_duplicated_names_skipped` | 重复工具名被跳过 |
| `test_qualify_tools_long_names_same_server` | 同服务器长工具名被截断并哈希 |
| `test_qualify_tools_sanitizes_invalid_characters` | 工具名中无效字符被净化 |
| `tool_filter_allows_by_default` | 工具过滤器默认允许所有 |
| `tool_filter_applies_enabled_list` | 工具过滤器应用启用列表 |
| `tool_filter_applies_disabled_list` | 工具过滤器应用禁用列表 |
| `tool_filter_applies_enabled_then_disabled` | 工具过滤器先应用启用再应用禁用列表 |
| `filter_tools_applies_per_server_filters` | 按服务器应用工具过滤器 |
| `codex_apps_tools_cache_is_overwritten_by_last_write` | Codex Apps 工具缓存被最后写入覆盖 |
| `codex_apps_tools_cache_is_scoped_per_user` | Codex Apps 工具缓存按用户隔离 |
| `codex_apps_tools_cache_filters_disallowed_connectors` | Codex Apps 工具缓存过滤不允许的连接器 |
| `codex_apps_tools_cache_is_ignored_when_schema_version_mismatches` | 缓存 schema 版本不匹配时忽略缓存 |
| `codex_apps_tools_cache_is_ignored_when_json_is_invalid` | 缓存 JSON 无效时忽略缓存 |
| `startup_cached_codex_apps_tools_loads_from_disk_cache` | 启动时从磁盘缓存加载 Codex Apps 工具 |
| `list_all_tools_uses_startup_snapshot_while_client_is_pending` | 客户端待定时使用启动快照列出所有工具 |
| `list_all_tools_blocks_while_client_is_pending_without_startup_snapshot` | 无启动快照且客户端待定时阻塞工具列表 |
| `list_all_tools_does_not_block_when_startup_snapshot_cache_hit_is_empty` | 启动快照缓存命中为空时不阻塞 |
| `list_all_tools_uses_startup_snapshot_when_client_startup_fails` | 客户端启动失败时使用启动快照 |
| `elicitation_capability_enabled_only_for_codex_apps` | elicitation 能力仅为 Codex Apps 启用 |
| `mcp_init_error_display_prompts_for_github_pat` | MCP 初始化错误显示提示 GitHub PAT |
| `mcp_init_error_display_prompts_for_login_when_auth_required` | 需要认证时提示登录 |
| `mcp_init_error_display_reports_generic_errors` | 报告通用错误信息 |
| `mcp_init_error_display_includes_startup_timeout_hint` | 包含启动超时提示 |
| `transport_origin_extracts_http_origin` | 从 HTTP 传输提取来源 |
| `transport_origin_is_stdio_for_stdio_transport` | stdio 传输的来源为 "stdio" |
