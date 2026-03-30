# `tool_suggest.rs` — 工具建议处理器

## 功能概述
该文件实现了 `tool_suggest` 工具，允许模型向用户建议安装新的连接器（Connector）或插件（Plugin）。处理流程包括：验证建议参数、查询可发现的工具列表、构建 MCP elicitation 请求以获取用户确认、在用户同意后验证安装是否完成，并在成功时合并连接器选择到当前会话。支持连接器和插件两种工具类型，但 TUI 客户端暂不支持插件建议。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolSuggestHandler` | struct | 工具建议处理器 |
| `ToolSuggestArgs` | struct | 工具参数：tool_type, action_type, tool_id, suggest_reason |
| `ToolSuggestResult` | struct | 返回结果：completed, user_confirmed, 工具信息 |
| `ToolSuggestMeta` | struct | elicitation 请求的元数据，包含审批类型、工具信息和安装 URL |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation) -> Result<FunctionToolOutput, FunctionCallError>` | 核心处理：验证参数 -> 获取可发现工具 -> 构建 elicitation -> 等待用户确认 -> 验证安装完成 |
| `build_tool_suggestion_elicitation_request` | `fn build_tool_suggestion_elicitation_request(...) -> McpServerElicitationRequestParams` | 构建 MCP elicitation 表单请求 |
| `build_tool_suggestion_meta` | `fn build_tool_suggestion_meta(...) -> ToolSuggestMeta` | 构建工具建议的元数据结构 |
| `verify_tool_suggestion_completed` | `async fn verify_tool_suggestion_completed(session, turn, tool, auth) -> bool` | 根据工具类型（连接器/插件）验证安装是否成功 |
| `refresh_missing_suggested_connectors` | `async fn refresh_missing_suggested_connectors(...) -> Option<Vec<AppInfo>>` | 刷新并检查期望的连接器是否已被识别 |
| `verified_connector_suggestion_completed` | `fn verified_connector_suggestion_completed(tool_id, connectors) -> bool` | 检查连接器是否可访问 |
| `verified_plugin_suggestion_completed` | `fn verified_plugin_suggestion_completed(tool_id, config, plugins_manager) -> bool` | 检查插件是否已安装 |

## 依赖关系
- **引用的 crate**: `async_trait`, `codex_app_server_protocol`, `codex_rmcp_client`, `rmcp`, `serde`, `serde_json`, `tracing`
- **内部依赖**: `connectors`, `mcp`, `tools::context`, `tools::discoverable`, `tools::registry`, `tools::handlers::parse_arguments`
- **被引用方**: 工具注册表中注册为 `tool_suggest` 工具

## 实现备注
- 当前仅支持 `action_type="install"`，其他操作类型会返回错误
- TUI 客户端（`codex-tui`）不支持插件类型的工具建议
- 连接器建议完成后，使用 `merge_connector_selection` 将其加入会话
- 插件验证流程：先重新加载用户配置，再检查 marketplace 中的安装状态，同时刷新关联的连接器
- 如果 codex apps 工具缓存中没有新安装的连接器，会执行一次硬刷新
- elicitation 请求使用 `McpServerElicitationRequest::Form` 格式，包含空的 schema（仅需要确认/拒绝）
