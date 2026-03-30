# `plugins.rs` — 测试模块

## 测试覆盖范围
插件（Plugins）系统，包括插件技能注入、MCP 工具列表、插件提及引导、应用插件集成以及插件使用分析事件追踪等。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `capability_sections_render_in_developer_message_in_order` | 能力段（Apps、Skills、Plugins）按正确顺序渲染到 developer 消息中 |
| `explicit_plugin_mentions_inject_plugin_guidance` | 显式插件提及时注入插件技能、MCP 和应用引导信息 |
| `explicit_plugin_mentions_track_plugin_used_analytics` | 显式插件提及时追踪 codex_plugin_used 分析事件 |
| `plugin_mcp_tools_are_listed` | 插件 MCP 工具正确出现在工具列表中 |
