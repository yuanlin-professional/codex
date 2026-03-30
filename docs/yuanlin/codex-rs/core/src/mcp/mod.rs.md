# `mod.rs` — MCP 模块主入口与工具管理

## 功能概述

`mcp/mod.rs` 是 MCP（Model Context Protocol）子系统的核心模块，负责 MCP 服务器的配置聚合、工具名称的构建与解析、Codex Apps 内置 MCP 服务器的配置生成、以及 MCP 快照的采集。它将用户配置的 MCP 服务器、插件提供的 MCP 服务器和 Codex Apps 内置服务器整合为统一的有效服务器列表，并提供工具名称的限定名（qualified name）管理和按服务器分组功能。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `McpManager` | struct | MCP 管理器，封装插件管理器引用，提供服务器配置查询和工具来源追溯 |
| `ToolPluginProvenance` | struct | 工具-插件来源追溯，记录 connector ID 和 MCP 服务器名称到插件显示名的映射 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `sanitize_responses_api_tool_name` | `fn sanitize_responses_api_tool_name(name: &str) -> String` | 清理工具名称，将非法字符替换为 `_` 以满足 Responses API 要求 |
| `qualified_mcp_tool_name_prefix` | `fn qualified_mcp_tool_name_prefix(server_name: &str) -> String` | 生成格式为 `mcp__{server}__{tool}` 的限定工具名前缀 |
| `split_qualified_tool_name` | `fn split_qualified_tool_name(qualified_name: &str) -> Option<(String, String)>` | 解析限定工具名为 `(server_name, tool_name)` |
| `group_tools_by_server` | `fn group_tools_by_server(tools) -> HashMap<String, HashMap<String, Tool>>` | 将工具按服务器名称分组 |
| `McpManager::configured_servers` | `fn configured_servers(&self, config) -> HashMap<...>` | 获取所有已配置的 MCP 服务器（用户 + 插件） |
| `McpManager::effective_servers` | `fn effective_servers(&self, config, auth) -> HashMap<...>` | 获取有效的 MCP 服务器列表（含 Codex Apps） |
| `McpManager::tool_plugin_provenance` | `fn tool_plugin_provenance(&self, config) -> ToolPluginProvenance` | 获取工具到插件的来源映射 |
| `codex_apps_mcp_url` | `fn codex_apps_mcp_url(config) -> String` | 根据配置生成 Codex Apps MCP 端点 URL |
| `with_codex_apps_mcp` | `fn with_codex_apps_mcp(servers, connectors_enabled, auth, config) -> HashMap<...>` | 根据 connectors 启用状态插入或移除 Codex Apps 服务器 |
| `collect_mcp_snapshot` | `async fn collect_mcp_snapshot(config) -> McpListToolsResponseEvent` | 采集完整的 MCP 快照（工具、资源、资源模板、认证状态） |
| `collect_mcp_snapshot_from_manager` | `async fn collect_mcp_snapshot_from_manager(manager, auth_entries) -> McpListToolsResponseEvent` | 从已有连接管理器中采集 MCP 快照 |

## 依赖关系

- **引用的 crate**: `codex_protocol`, `codex_rmcp_client`, `codex_features`, `serde_json`, `async_channel`, `tracing`
- **引用的内部模块**: `auth`（OAuth 认证）、`skill_dependencies`（技能依赖安装）、`Config`、`McpServerConfig`、`McpConnectionManager`、`PluginsManager`、`AuthManager`、`CodexAuth`
- **被引用方**: `SessionServices`（持有 `McpManager`）、会话初始化流程、CLI 的 MCP 快照命令

## 实现备注

- MCP 工具名称遵循 `mcp__{server}__{tool}` 格式，分隔符为 `__`
- `sanitize_responses_api_tool_name` 确保名称匹配 `^[a-zA-Z0-9_-]+$` 正则，连字符 `-` 被替换为 `_`
- Codex Apps MCP URL 生成逻辑针对 `chatgpt.com`、`chat.openai.com` 和 `api/codex` 等多种 base URL 模式做了特殊处理
- `codex_apps_mcp_bearer_token_env_var` 优先检查环境变量 `CODEX_CONNECTORS_TOKEN`，有值时使用环境变量认证，否则从 `CodexAuth` 获取 token
- `collect_mcp_snapshot` 使用 `ReadOnly` 沙箱策略作为采集时的安全默认值
- 插件提供的 MCP 服务器不会覆盖用户已配置的同名服务器（`entry().or_insert()`）
