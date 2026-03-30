# `mcp_resource.rs` — MCP 资源操作处理器

## 功能概述
该文件实现了 `McpResourceHandler`，用于处理 MCP 资源相关的三种操作：列出资源 (`list_mcp_resources`)、列出资源模板 (`list_mcp_resource_templates`) 和读取资源 (`read_mcp_resource`)。处理器根据工具名称分派到对应的异步处理函数，支持单服务器查询和跨所有服务器的聚合查询，并在每次调用前后发射 `McpToolCallBegin` / `McpToolCallEnd` 事件以供 UI 和遥测使用。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `McpResourceHandler` | struct | MCP 资源工具调用的主处理器，实现 `ToolHandler` trait |
| `ListResourcesArgs` | struct | `list_mcp_resources` 的反序列化参数，包含可选的 `server` 和 `cursor` |
| `ListResourceTemplatesArgs` | struct | `list_mcp_resource_templates` 的反序列化参数 |
| `ReadResourceArgs` | struct | `read_mcp_resource` 的反序列化参数，包含 `server` 和 `uri` |
| `ResourceWithServer` | struct | 将 `Resource` 与所属服务器名称组合，序列化时使用 `#[serde(flatten)]` 展平 |
| `ResourceTemplateWithServer` | struct | 将 `ResourceTemplate` 与所属服务器名称组合 |
| `ListResourcesPayload` | struct | 列出资源的响应载荷，支持单服务器（含分页游标）和全服务器模式 |
| `ListResourceTemplatesPayload` | struct | 列出资源模板的响应载荷 |
| `ReadResourcePayload` | struct | 读取资源的响应载荷 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<FunctionToolOutput, FunctionCallError>` | 根据 `tool_name` 分派到三个子处理函数 |
| `handle_list_resources` | `async fn(session, turn, call_id, arguments) -> Result<FunctionToolOutput, _>` | 列出资源，支持单服务器分页和全服务器聚合 |
| `handle_list_resource_templates` | `async fn(session, turn, call_id, arguments) -> Result<FunctionToolOutput, _>` | 列出资源模板，逻辑与 `handle_list_resources` 类似 |
| `handle_read_resource` | `async fn(session, turn, call_id, arguments) -> Result<FunctionToolOutput, _>` | 通过 URI 读取指定服务器上的资源 |
| `emit_tool_call_begin` | `async fn(session, turn, call_id, invocation)` | 发射 MCP 工具调用开始事件 |
| `emit_tool_call_end` | `async fn(session, turn, call_id, invocation, duration, result)` | 发射 MCP 工具调用结束事件，包含耗时和结果 |
| `normalize_optional_string` | `fn(Option<String>) -> Option<String>` | 对可选字符串进行 trim 处理，空字符串转为 None |
| `normalize_required_string` | `fn(field, value) -> Result<String, _>` | 对必填字符串进行归一化，空值返回错误 |
| `serialize_function_output` | `fn<T: Serialize>(payload) -> Result<FunctionToolOutput, _>` | 将响应载荷序列化为 JSON 文本并包装为 `FunctionToolOutput` |
| `parse_arguments` | `fn(raw_args) -> Result<Option<Value>, _>` | 解析 JSON 字符串参数，空或 null 返回 None |
| `parse_args` / `parse_args_with_default` | `fn<T>(Option<Value>) -> Result<T, _>` | 从 `Value` 反序列化为具体类型，后者在 None 时使用 Default |

## 依赖关系
- **引用的 crate**: `async_trait`, `rmcp::model::*`, `serde`, `serde_json`, `codex_protocol::mcp::CallToolResult`, `codex_protocol::models::*`
- **引用的内部模块**: `crate::codex::Session`, `crate::codex::TurnContext`, `crate::protocol::*`, `crate::tools::context::*`, `crate::tools::registry::*`
- **被引用方**: `mod.rs` 通过 `pub use mcp_resource::McpResourceHandler` 导出
- **关联测试**: `mcp_resource_tests.rs`

## 实现备注
- 全服务器聚合查询时，按服务器名称字典序排序以确保输出稳定
- 分页游标 (`cursor`) 仅在指定了具体 `server` 时可用，否则返回错误
- 每次操作都通过事件系统上报调用开始/结束，包含调用时长信息
- 响应格式使用 `camelCase` 序列化以符合 JSON 惯例
