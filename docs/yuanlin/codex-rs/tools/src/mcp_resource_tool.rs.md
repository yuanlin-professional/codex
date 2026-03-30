# `mcp_resource_tool.rs` -- MCP 资源操作工具定义

## 功能概述
定义三个 MCP 资源操作工具：`list_mcp_resources`（列出 MCP 服务器提供的资源）、`list_mcp_resource_templates`（列出资源模板）和 `read_mcp_resource`（读取指定 URI 的资源内容）。资源为 LLM 提供文件、数据库 schema 等上下文数据。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_list_mcp_resources_tool` | `fn() -> ToolSpec` | 列出资源工具，可选服务器名和分页游标 |
| `create_list_mcp_resource_templates_tool` | `fn() -> ToolSpec` | 列出资源模板工具 |
| `create_read_mcp_resource_tool` | `fn() -> ToolSpec` | 读取资源工具，需要 server 和 uri 参数 |

## 依赖关系
- **被引用方**: `lib.rs` 公开导出
