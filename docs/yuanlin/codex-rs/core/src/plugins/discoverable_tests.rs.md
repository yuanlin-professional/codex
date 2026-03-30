# `discoverable_tests.rs` -- 测试模块

## 测试覆盖范围
测试 `discoverable.rs` 中的 `list_tool_suggest_discoverable_plugins` 函数在各种条件下的行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `list_tool_suggest_discoverable_plugins_returns_uninstalled_curated_plugins` | 验证仅返回白名单中的未安装精选插件（如 slack），非白名单插件（如 sample）被过滤 |
| `list_tool_suggest_discoverable_plugins_returns_empty_when_plugins_feature_disabled` | 验证插件功能禁用时返回空列表 |
| `list_tool_suggest_discoverable_plugins_normalizes_description` | 验证插件描述中的多余空白字符会被规范化为单个空格 |
| `list_tool_suggest_discoverable_plugins_omits_installed_curated_plugins` | 验证已安装的插件不会出现在可发现列表中 |
| `list_tool_suggest_discoverable_plugins_includes_configured_plugin_ids` | 验证通过 `tool_suggest.discoverables` 配置的插件 ID 即使不在白名单中也会被包含 |
