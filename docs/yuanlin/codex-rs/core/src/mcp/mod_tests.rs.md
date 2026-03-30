# `mod_tests.rs` — 测试模块

## 测试覆盖范围

针对 `mcp/mod.rs` 中的工具名称解析/构建、工具按服务器分组、工具-插件来源追溯、Codex Apps MCP URL 生成、Codex Apps 服务器配置、以及插件 MCP 服务器与用户配置的合并逻辑进行测试。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `split_qualified_tool_name_returns_server_and_tool` | 验证限定工具名 `mcp__alpha__do_thing` 正确解析为 `("alpha", "do_thing")` |
| `qualified_mcp_tool_name_prefix_sanitizes_server_names_without_lowercasing` | 验证服务器名中的 `-` 被替换为 `_`，但不改变大小写 |
| `split_qualified_tool_name_rejects_invalid_names` | 验证非 `mcp` 前缀和空工具名被正确拒绝 |
| `group_tools_by_server_strips_prefix_and_groups` | 验证工具按服务器名称正确分组，支持嵌套 `__` 分隔的工具名 |
| `tool_plugin_provenance_collects_app_and_mcp_sources` | 验证从插件能力摘要正确构建 connector ID 和 MCP 服务器名到插件名的映射 |
| `codex_apps_mcp_url_for_base_url_keeps_existing_paths` | 验证多种 base URL 格式下 Codex Apps MCP URL 的生成正确性 |
| `codex_apps_mcp_url_uses_legacy_codex_apps_path` | 验证 `chatgpt.com` base URL 生成 `/backend-api/wham/apps` 路径 |
| `codex_apps_server_config_uses_legacy_codex_apps_path` | 验证 Codex Apps 服务器配置在 connectors 启用/禁用时的正确行为 |
| `effective_mcp_servers_include_plugins_without_overriding_user_config` | 验证插件提供的 MCP 服务器被包含，但不覆盖用户已配置的同名服务器 |
