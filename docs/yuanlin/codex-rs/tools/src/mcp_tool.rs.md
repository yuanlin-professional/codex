# `mcp_tool.rs` -- MCP 工具解析

## 功能概述
将 rmcp SDK 的 `Tool` 结构体解析为 `ToolDefinition`。自动为缺少 `properties` 字段的 input_schema 补充空对象。将 MCP 的 output_schema 包装为标准的 `CallToolResult` 格式（包含 content 数组、structuredContent、isError 和 _meta 字段）。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_mcp_tool` | `fn(tool) -> Result<ToolDefinition>` | MCP Tool 转 ToolDefinition |
| `mcp_call_tool_result_output_schema` | `fn(structured_content_schema) -> JsonValue` | 构建 CallToolResult 格式的 output_schema |

## 依赖关系
- **引用的 crate**: `rmcp`
- **被引用方**: `lib.rs` 公开导出, `responses_api.rs` 使用
