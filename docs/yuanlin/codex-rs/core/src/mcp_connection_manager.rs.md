# `mcp_connection_manager.rs` — MCP 服务器连接管理器

## 功能概述

`mcp_connection_manager.rs`（约 1696 行）负责管理 Model Context Protocol（MCP）服务器的连接生命周期。`McpConnectionManager` 持有每个配置的 MCP 服务器对应的 `RmcpClient` 实例（按服务器名称索引），提供工具列表聚合、工具调用分发、资源列表/读取、沙箱状态通知、elicitation 请求解析等功能。它还实现了 Codex Apps 工具缓存（磁盘缓存 + 内存缓存）、工具过滤、启动快照、异步初始化等特性。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `McpConnectionManager` | struct | 连接管理器，持有 clients HashMap、server_origins、ElicitationRequestManager |
| `ToolInfo` | struct | 工具信息，包含 server_name、tool_name、tool_namespace、tool 定义、connector_id/name、plugin_display_names |
| `ManagedClient` | struct | 已初始化的 MCP 客户端包装，含工具列表、工具过滤器、超时配置、沙箱状态能力标志、工具缓存上下文 |
| `AsyncManagedClient` | struct | 异步初始化的 MCP 客户端，使用 `Shared<BoxFuture>` 延迟初始化，支持启动快照 |
| `ToolFilter` | struct | 工具过滤器，支持 enabled allowlist 和 disabled blocklist |
| `SandboxState` | struct | 沙箱状态，用于推送给 MCP 服务器 |
| `ElicitationRequestManager` | struct | Elicitation 请求管理器，管理待处理的 elicitation 请求/响应对 |
| `CodexAppsToolsCacheKey` | struct | Codex Apps 工具缓存键（account_id、chatgpt_user_id、is_workspace_account） |
| `CodexAppsToolsDiskCache` | struct | 磁盘缓存格式 |
| `StartupOutcomeError` | enum | 启动结果错误：Cancelled 或 Failed |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `McpConnectionManager::new` | `async fn new(mcp_servers, store_mode, ...) -> (Self, CancellationToken)` | 创建管理器，并行启动所有 MCP 服务器连接 |
| `McpConnectionManager::list_all_tools` | `async fn list_all_tools(&self) -> HashMap<String, ToolInfo>` | 返回所有服务器的聚合工具列表（全限定名为键） |
| `McpConnectionManager::call_tool` | `async fn call_tool(server, tool, arguments, meta)` | 调用指定服务器上的工具 |
| `McpConnectionManager::list_resources` / `list_resource_templates` / `read_resource` | `async fn ...` | 资源列表/模板/读取 |
| `McpConnectionManager::hard_refresh_codex_apps_tools_cache` | `async fn ...` | 强制刷新 Codex Apps 工具缓存 |
| `McpConnectionManager::notify_sandbox_state_change` | `async fn ...` | 通知所有服务器沙箱状态变更 |
| `McpConnectionManager::resolve_elicitation` | `async fn ...` | 解析 elicitation 响应 |
| `qualify_tools` | `fn qualify_tools(tools) -> HashMap<String, ToolInfo>` | 生成全限定工具名，处理命名冲突和长度限制 |
| `start_server_task` | `async fn start_server_task(...)` | 初始化单个 MCP 服务器：连接、初始化、获取工具列表 |
| `make_rmcp_client` | `async fn make_rmcp_client(...)` | 根据传输配置（Stdio/StreamableHttp）创建 RMCP 客户端 |
| `filter_non_codex_apps_mcp_tools_only` | `fn ...` | 过滤出非 codex_apps 的 MCP 工具 |

## 依赖关系

- **引用的 crate**: `codex_rmcp_client`（RMCP 客户端）、`rmcp`（MCP 协议模型）、`codex_protocol`（协议类型）、`codex_async_utils`（取消扩展）、`sha1`（缓存哈希）、`tokio`、`futures`
- **被引用方**: `codex.rs`（Session 持有 `Arc<RwLock<McpConnectionManager>>`）、`mcp_tool_call.rs`（工具调用）、`connectors.rs`（连接器列表）

## 实现备注

1. **全限定工具名**：格式为 `mcp__<server>__<tool>`，对 codex_apps 使用 `mcp__codex_apps__<connector>__<tool>` 格式。工具名超过 64 字符时使用 SHA1 截断。
2. **启动快照**：对 codex_apps 服务器，若存在磁盘缓存则立即返回启动快照，同时后台初始化真实连接。
3. **工具插件标注**：工具描述中自动追加 "This tool is part of plugin `...`" 标注。
4. **Elicitation 请求**：通过 oneshot channel 实现异步 elicitation 请求/响应对，支持 form 和 url 两种类型。
5. **沙箱状态推送**：通过自定义 MCP 请求 `codex/sandbox-state/update` 将沙箱状态推送给支持此能力的服务器。
