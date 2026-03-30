# `tool_spec_tests.rs` -- 测试模块

## 测试覆盖范围
验证 ToolSpec 枚举各变体的 JSON 序列化格式和 create_tools_json_for_responses_api 的批量转换。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `function_tool_serializes` | Function 变体序列化格式验证 |
| `web_search_tool_serializes` | WebSearch 变体序列化 |
| `web_search_tool_with_filters` | WebSearch 带过滤器序列化 |
| `local_shell_tool_serializes` | LocalShell 变体序列化 |
| `image_generation_tool_serializes` | ImageGeneration 变体序列化 |
| `freeform_tool_serializes` | Freeform 变体序列化 |
| `create_tools_json_for_responses_api_batch` | 批量转换多个 ToolSpec |
