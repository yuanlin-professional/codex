# `mcp.rs` — MCP 工具调用处理器

## 功能概述
该文件实现了 `McpHandler` 结构体，用于处理 MCP (Model Context Protocol) 工具调用请求。它从 `ToolInvocation` 中提取 MCP 特有的载荷信息（服务器名称、工具名称、原始参数），然后委托给 `handle_mcp_tool_call` 函数执行实际的 MCP 工具调用，并返回 `CallToolResult`。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `McpHandler` | struct | MCP 工具调用的处理器，实现了 `ToolHandler` trait |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `kind` | `fn kind(&self) -> ToolKind` | 返回 `ToolKind::Mcp`，标识该处理器属于 MCP 类型 |
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<CallToolResult, FunctionCallError>` | 解构 MCP 载荷并委托给 `handle_mcp_tool_call` 执行远程工具调用 |

## 依赖关系
- **引用的 crate**: `async_trait`, `codex_protocol::mcp::CallToolResult`
- **引用的内部模块**: `crate::function_tool::FunctionCallError`, `crate::mcp_tool_call::handle_mcp_tool_call`, `crate::tools::context::*`, `crate::tools::registry::*`
- **被引用方**: `mod.rs` 中通过 `pub use mcp::McpHandler` 导出

## 实现备注
- 处理器仅接受 `ToolPayload::Mcp` 类型的载荷，对其他类型返回错误
- 实际的 MCP 调用逻辑封装在 `handle_mcp_tool_call` 中，本文件仅负责参数解包和转发
- 使用 `Arc::clone` 共享 `session` 引用以避免不必要的所有权转移
