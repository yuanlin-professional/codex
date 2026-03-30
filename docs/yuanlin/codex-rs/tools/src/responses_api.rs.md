# `responses_api.rs` -- Responses API 工具格式转换

## 功能概述
定义 Responses API 工具格式（`ResponsesApiTool`、`FreeformTool`、命名空间工具）并提供 MCP 工具、动态工具和 ToolDefinition 到 Responses API 格式的转换函数。支持工具命名空间（如 `connector.tool_name`）、延迟加载和 output_schema 传递。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ResponsesApiTool` | struct | 函数类型工具定义，包含名称、描述、parameters schema 和可选 output_schema |
| `FreeformTool` | struct | 自定义格式工具（如 js_repl），使用语法定义而非 JSON schema |
| `FreeformToolFormat` | struct | Freeform 工具格式：type、syntax 和 definition |
| `ResponsesApiNamespace` | struct | 工具命名空间，包含连接器 ID/名称/描述 |
| `ResponsesApiNamespaceTool` | struct | 带命名空间的工具 |
| `ToolSearchOutputTool` | struct | 工具搜索结果输出 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `mcp_tool_to_responses_api_tool` | `fn(tool, namespace) -> Result<ResponsesApiNamespaceTool>` | MCP 工具转 API 格式 |
| `dynamic_tool_to_responses_api_tool` | `fn(tool) -> Result<ResponsesApiTool>` | 动态工具转 API 格式 |
| `tool_definition_to_responses_api_tool` | `fn(definition) -> ResponsesApiTool` | ToolDefinition 转 API 格式 |

## 依赖关系
- **引用的 crate**: `serde`, `codex_protocol`
- **被引用方**: codex core 构建 API 请求
