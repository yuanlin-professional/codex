# mcp/ -- MCP 集成

## 功能概述

`mcp/` 是 Codex 的 Model Context Protocol (MCP) 集成模块。它负责 MCP 服务器的配置管理、工具名称限定和清洗、OAuth 认证流程、MCP 工具快照收集，以及 MCP 技能依赖的安装提示。MCP 是 Codex 与外部工具服务器通信的标准协议，该模块将用户配置的 MCP 服务器（包括内置的 `codex_apps` 服务器）统一管理，并为工具系统提供合格的工具列表。

## 目录结构

```
mcp/
├── mod.rs                        # McpManager、工具名称处理、MCP 服务器配置组装、快照收集
├── auth.rs                       # MCP OAuth 认证：登录支持检测、OAuth scope 解析、认证状态计算
├── skill_dependencies.rs         # MCP 技能依赖安装提示（检测缺失依赖、提示用户安装）
├── mod_tests.rs                  # 模块测试
└── skill_dependencies_tests.rs   # 技能依赖测试
```

## 依赖关系

- **上游依赖**：`codex-protocol`（MCP 类型 `Tool`、`Resource`、`ResourceTemplate`、`McpAuthStatus`）、`codex-rmcp-client`（OAuth 登录、凭据存储）、`rmcp`（MCP 协议工具类型）
- **内部依赖**：`config/`（Config、McpServerConfig）、`plugins/`（PluginsManager、PluginCapabilitySummary）、`mcp_connection_manager`（MCP 连接管理器）、`auth`（AuthManager、CodexAuth）
- **被依赖方**：`state/`（SessionServices 持有 McpManager）、`tools/`（工具注册时使用 MCP 工具列表）、`codex/`（Session 初始化 MCP 连接）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `McpManager` | MCP 管理器，提供 `configured_servers()`、`effective_servers()`、`tool_plugin_provenance()` |
| `ToolPluginProvenance` | 工具来源追踪：按 connector_id 和 MCP server name 映射插件显示名 |
| `sanitize_responses_api_tool_name()` | 清洗工具名称使其符合 Responses API 约束（`^[a-zA-Z0-9_-]+$`） |
| `qualified_mcp_tool_name_prefix()` | 生成限定 MCP 工具名前缀（`mcp__{server}__{tool}`） |
| `split_qualified_tool_name()` | 分割限定工具名为 (server_name, tool_name) |
| `collect_mcp_snapshot()` | 收集所有 MCP 服务器的工具/资源/资源模板快照 |
| `with_codex_apps_mcp()` | 根据连接器启用状态注入/移除内置 codex_apps MCP 服务器 |
| `maybe_prompt_and_install_mcp_dependencies()` | 检测并提示安装缺失的 MCP 技能依赖 |
