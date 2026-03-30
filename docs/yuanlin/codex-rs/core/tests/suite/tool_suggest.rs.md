# `tool_suggest.rs` -- 测试模块

## 测试覆盖范围
测试 tool_suggest 工具的启用与描述逻辑。验证在未启用 tool_search 且配置了 discoverables 的情况下，tool_suggest 工具可用并包含正确的描述文本。仅在非 Windows 系统上运行。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `tool_suggest_is_available_without_search_tool_after_discovery_attempts` | 启用 Apps + Plugins + ToolSuggest feature（不启用 ToolSearch），请求中包含 tool_suggest 但不包含 tool_search；描述文本包含 "already tried to find a matching available tool" 和 "tool_search (if available)" |
