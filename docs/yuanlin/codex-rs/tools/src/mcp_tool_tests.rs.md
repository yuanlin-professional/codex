# `mcp_tool_tests.rs` -- 测试模块

## 测试覆盖范围
验证 MCP 工具解析逻辑，包括缺少 properties 字段的处理、output_schema 的保留和传递。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `parse_mcp_tool_inserts_empty_properties` | 当 MCP 工具 input_schema 缺少 properties 时，自动插入空对象 |
| `parse_mcp_tool_preserves_top_level_output_schema` | 保留工具的 output_schema 并正确包装为 CallToolResult 格式 |
| `parse_mcp_tool_preserves_output_schema_without_inferred_type` | 对于 enum 类型的 output_schema 不添加推断的 type 字段 |
