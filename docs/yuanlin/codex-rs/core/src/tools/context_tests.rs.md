# `context_tests.rs` — 测试模块

## 测试覆盖范围
验证工具上下文中各类输出类型的序列化、响应映射和遥测预览逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `custom_tool_calls_should_roundtrip_as_custom_outputs` | Custom 载荷的输出映射为 CustomToolCallOutput |
| `function_payloads_remain_function_outputs` | Function 载荷保持为 FunctionCallOutput |
| `mcp_code_mode_result_serializes_full_call_tool_result` | MCP 结果包含 structuredContent 时的 Code Mode 序列化 |
| `custom_tool_calls_can_derive_text_from_content_items` | 包含多项内容的 Custom 输出正确派生文本 |
| `tool_search_payloads_roundtrip_as_tool_search_outputs` | ToolSearch 载荷映射为 ToolSearchOutput |
| `log_preview_uses_content_items_when_plain_text_is_missing` | 从 content_items 生成日志预览 |
| `telemetry_preview_returns_original_within_limits` | 短文本原样返回 |
| `telemetry_preview_truncates_by_bytes` | 超出字节限制时截断并添加通知 |
| `telemetry_preview_truncates_by_lines` | 超出行数限制时截断 |
| `exec_command_tool_output_formats_truncated_response` | ExecCommandToolOutput 的完整响应格式化验证 |
