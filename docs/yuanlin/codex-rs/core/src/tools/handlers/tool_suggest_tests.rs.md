# `tool_suggest_tests.rs` — 测试模块

## 测试覆盖范围
测试覆盖了 `tool_suggest` 工具的 elicitation 请求构建、元数据结构序列化、客户端过滤逻辑、连接器/插件安装验证等功能，确保工具建议流程在不同工具类型和客户端环境下行为正确。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `build_tool_suggestion_elicitation_request_uses_expected_shape` | 验证连接器类型的 elicitation 请求包含正确的 thread_id、server_name、meta（含 install_url）和空 schema |
| `build_tool_suggestion_elicitation_request_for_plugin_omits_install_url` | 验证插件类型的 elicitation 请求中 meta 的 `install_url` 为 None |
| `build_tool_suggestion_meta_uses_expected_shape` | 验证 `ToolSuggestMeta` 结构体包含正确的 `codex_approval_kind` 和所有工具信息字段 |
| `filter_tool_suggest_discoverable_tools_for_codex_tui_omits_plugins` | 验证 TUI 客户端过滤后仅保留连接器类型，移除插件类型 |
| `verified_connector_suggestion_completed_requires_accessible_connector` | 验证连接器安装检查要求 `is_accessible=true` 且 tool_id 匹配 |
| `all_suggested_connectors_picked_up_requires_every_expected_connector` | 验证多连接器场景下需要所有期望的连接器都被识别 |
| `verified_plugin_suggestion_completed_requires_installed_plugin` | 验证插件安装检查通过 PluginsManager 确认安装状态，安装前返回 false，安装后返回 true |
