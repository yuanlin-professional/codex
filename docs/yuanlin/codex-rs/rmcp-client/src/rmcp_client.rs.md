# `rmcp_client.rs` -- MCP 客户端核心实现

## 功能概述
基于官方 rmcp SDK 实现的 MCP 客户端，支持 stdio 和 Streamable HTTP 两种传输方式。提供完整的 MCP 协议操作（初始化、工具列表/调用、资源列表/读取/模板、自定义请求/通知），内置 OAuth 令牌管理和自动刷新机制，以及 HTTP 404 会话过期的自动恢复重连逻辑。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `RmcpClient` | struct | MCP 客户端主结构体，管理连接状态、传输配方和会话恢复锁 |
| `ClientState` | enum | 客户端状态：Connecting（连接中）或 Ready（就绪） |
| `PendingTransport` | enum | 待连接传输：子进程/Streamable HTTP/带 OAuth 的 Streamable HTTP |
| `TransportRecipe` | enum | 传输配方：Stdio 或 StreamableHttp，用于重连时重建传输 |
| `ProcessGroupGuard` | struct | Unix 进程组守护，drop 时终止整个进程组 |
| `ElicitationResponse` | struct | elicitation 响应，包含动作、内容和元数据 |
| `ToolWithConnectorId` | struct | 带 connector 信息的工具定义 |
| `StreamableHttpResponseClient` | struct | 自定义 reqwest HTTP 客户端适配器 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new_stdio_client` | `async fn(program, args, env, ...) -> io::Result<Self>` | 创建 stdio 传输的 MCP 客户端 |
| `new_streamable_http_client` | `async fn(server_name, url, ...) -> Result<Self>` | 创建 Streamable HTTP 传输的 MCP 客户端 |
| `initialize` | `async fn(&self, params, timeout, send_elicitation) -> Result<InitializeResult>` | 执行 MCP 初始化握手 |
| `call_tool` | `async fn(&self, name, arguments, meta, timeout) -> Result<CallToolResult>` | 调用 MCP 工具 |
| `list_tools_with_connector_ids` | `async fn(&self, ...) -> Result<ListToolsWithConnectorIdResult>` | 列出工具并提取 connector 元信息 |
| `reinitialize_after_session_expiry` | `async fn(&self, failed_service) -> Result<()>` | 404 会话过期后自动重建连接 |

## 依赖关系
- **引用的 crate**: `rmcp`, `reqwest`, `tokio`, `futures`, `oauth2`, `sse_stream`
- **被引用方**: `lib.rs` 导出，被 codex core 和 TUI 层使用

## 实现备注
- 会话恢复使用 `session_recovery_lock` 防止并发重连
- OAuth 令牌在每次操作前检查刷新、操作后持久化
- stdio 模式下通过 `process_group(0)` 创建独立进程组，drop 时发送 SIGTERM 并在 2 秒后升级为 SIGKILL
- Streamable HTTP 模式下支持 SSE 和 JSON 两种响应格式
