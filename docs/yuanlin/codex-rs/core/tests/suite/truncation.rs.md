# `truncation.rs` -- 测试模块

## 测试覆盖范围
全面测试工具调用输出的截断策略，包括 shell_command 和 MCP 工具输出在不同配置下的截断行为。覆盖字符/token 两种截断模式、配置化 token 限制、截断标记唯一性、MCP 图片输出保留以及大预算下不截断等场景。仅在非 Windows 系统上运行。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `tool_call_output_configured_limit_chars_type` | 配置 100k token 限制时，100k 行 shell 输出不被截断（约 400k 字符） |
| `tool_call_output_exceeds_limit_truncated_chars_limit` | 默认 token 限制下，100k 行 shell 输出被截断到约 10k 字符，包含 "chars truncated" 标记 |
| `tool_call_output_exceeds_limit_truncated_for_model` | gpt-5.1-codex 模型下，100k 行输出被按 token 截断，保留首尾行，中间显示 "tokens truncated" |
| `tool_call_output_truncated_only_once` | 截断标记仅出现一次，不重复截断 |
| `mcp_tool_call_output_exceeds_limit_truncated_for_model` | MCP echo 工具输出超限时按 token 截断，不包含行级截断头 |
| `mcp_image_output_preserves_image_and_no_text_summary` | MCP 图片输出保持 input_image 格式，不添加截断摘要 |
| `token_policy_marker_reports_tokens` | token 策略下截断标记报告 token 数量 |
| `byte_policy_marker_reports_bytes` | byte 策略下截断标记报告字符数量 |
| `shell_command_output_not_truncated_with_custom_limit` | 自定义 50k token 限制下 1000 行输出不被截断 |
| `mcp_tool_call_output_not_truncated_with_custom_limit` | 自定义 50k token 限制下 80k 字符 MCP 输出不被截断 |
