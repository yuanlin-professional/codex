# `search_tool.rs` -- 测试模块

## 测试覆盖范围
测试 tool_search（工具搜索）功能的集成行为，包括工具搜索的启用/禁用、搜索结果中 deferred tools 的处理、应用提及（app mention）对工具暴露的影响，以及 tool_search 描述中包含发现指引等。该测试依赖 AppsTestServer 模拟 ChatGPT Apps 后端。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `search_tool_flag_adds_tool_search` | 启用 ToolSearch feature 后，请求中包含 tool_search 工具，并验证其 JSON schema 结构正确 |
| `tool_search_disabled_by_default_exposes_apps_tools_directly` | 未启用 ToolSearch 时，apps 工具（如 calendar_create_event）直接暴露，不包含 tool_search |
| `search_tool_is_hidden_for_api_key_auth` | 使用 API Key 认证时，即使启用了 ToolSearch feature，也不会暴露 tool_search 工具 |
| `search_tool_adds_discovery_instructions_to_tool_description` | tool_search 描述中包含应用/连接器发现指引文本 |
| `search_tool_hides_apps_tools_without_search` | 启用 tool_search 后，apps 工具在搜索前被隐藏 |
| `explicit_app_mentions_expose_apps_tools_without_search` | 用户消息中显式 @mention app 时，对应 apps 工具被直接暴露 |
| `tool_search_returns_deferred_tools_without_follow_up_tool_injection` | tool_search 返回 deferred tools 后，后续请求不额外注入 app 工具，而是依赖 tool_search_output 历史 |
