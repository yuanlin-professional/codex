# `dynamic_tool.rs` -- 动态工具解析

动态工具（`DynamicToolSpec`）到 `ToolDefinition` 的解析函数。从协议定义的动态工具规格中提取名称、描述和 input_schema，通过 `parse_tool_input_schema` 将 JSON 值转换为类型化的 `JsonSchema` 枚举。output_schema 固定为 None，defer_loading 从源规格传递。
