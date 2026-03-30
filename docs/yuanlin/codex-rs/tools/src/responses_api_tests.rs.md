# `responses_api_tests.rs` -- 测试模块

## 测试覆盖范围
验证工具定义到 Responses API 格式的转换逻辑，包括 MCP 工具、动态工具和延迟加载工具。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `mcp_tool_to_responses_api_tool_basic` | MCP 工具转换为 ResponsesApiTool 的基本流程 |
| `dynamic_tool_to_responses_api_tool_basic` | 动态工具转换为 ResponsesApiTool |
| `mcp_tool_to_deferred_tool` | 延迟加载 MCP 工具转换 |
| `tool_definition_to_responses_api_tool_with_output_schema` | 包含 output_schema 的工具转换 |
