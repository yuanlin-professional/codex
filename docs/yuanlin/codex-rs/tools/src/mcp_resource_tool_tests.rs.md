# `mcp_resource_tool_tests.rs` -- 测试模块

## 测试覆盖范围
验证 MCP 资源工具的 ToolSpec 定义和 JSON 序列化格式。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `list_mcp_resources_tool_serializes` | 验证 list_mcp_resources 工具序列化包含 server 和 cursor 参数 |
| `list_mcp_resource_templates_tool_serializes` | 验证 list_mcp_resource_templates 工具序列化 |
| `read_mcp_resource_tool_serializes` | 验证 read_mcp_resource 工具序列化包含 server 和 uri 必填参数 |
