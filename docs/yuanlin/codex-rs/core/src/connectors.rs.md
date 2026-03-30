# `connectors.rs` — 连接器管理：App 发现、启用状态与工具策略

## 功能概述

`connectors.rs`（约 991 行）实现了 Codex 的连接器（Connectors/Apps）管理系统，负责从 MCP 工具列表中提取可访问的连接器信息、管理连接器的启用/禁用状态、工具级别的策略控制（enabled/approval）、连接器缓存、目录连接器发现（用于 tool_suggest）以及连接器合并/过滤。它是 Codex Apps 功能的核心基础设施，连接 MCP 工具系统和用户可见的 App 界面。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppToolPolicy` | struct | App 工具策略：enabled(bool) + approval(AppToolApproval) |
| `AccessibleConnectorsStatus` | struct | 可访问连接器状态：connectors 列表 + codex_apps_ready 标志 |
| `AccessibleConnectorsCacheKey` | struct | 连接器缓存键：chatgpt_base_url、account_id、chatgpt_user_id、is_workspace_account |
| `CachedAccessibleConnectors` | struct | 缓存的连接器数据 |
| `AppInfo` | struct (re-export) | App 信息：id、name、description、logo_url、is_accessible、is_enabled、plugin_display_names 等 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `list_accessible_connectors_from_mcp_tools` | `async fn ...(config) -> Result<Vec<AppInfo>>` | 从 MCP 工具列表获取可访问连接器 |
| `list_accessible_connectors_from_mcp_tools_with_options_and_status` | `async fn ...` | 带选项的完整连接器发现流程（含缓存、MCP 启动等待） |
| `list_cached_accessible_connectors_from_mcp_tools` | `async fn ...` | 仅从缓存读取连接器 |
| `accessible_connectors_from_mcp_tools` | `fn ...(mcp_tools) -> Vec<AppInfo>` | 从 MCP 工具 HashMap 提取连接器列表 |
| `list_accessible_and_enabled_connectors_from_manager` | `async fn ...` | 列出已启用且可访问的连接器 |
| `list_tool_suggest_discoverable_tools_with_auth` | `async fn ...` | 获取 tool_suggest 可发现工具（目录连接器 + 插件） |
| `refresh_accessible_connectors_cache_from_mcp_tools` | `fn ...` | 从 MCP 工具刷新缓存 |
| `app_tool_policy` | `fn ...(config, connector_id, tool_name, tool_title, annotations) -> AppToolPolicy` | 获取工具策略（enabled + approval） |
| `with_app_enabled_state` | `fn ...(connectors, config) -> Vec<AppInfo>` | 应用用户/requirements 配置的启用状态 |
| `merge_connectors` | `fn ...(connectors, accessible) -> Vec<AppInfo>` | 合并目录连接器和可访问连接器 |
| `merge_plugin_apps` | `fn ...` | 合并插件 App |
| `filter_disallowed_connectors` | `fn ...` | 过滤黑名单连接器 |
| `is_connector_id_allowed` | `fn ...` | 判断连接器 ID 是否被允许 |
| `connector_display_label` / `connector_mention_slug` / `sanitize_name` | `fn ...` | 连接器显示名/提及 slug/名称清洗 |
| `connector_install_url` | `fn ...` | 生成连接器安装 URL |

## 依赖关系

- **引用的 crate**: `codex_connectors`（连接器底层缓存和目录 API）、`codex_app_server_protocol`（AppInfo 类型）、`codex_protocol`（SandboxPolicy）、`codex_features`（Feature::Apps）
- **被引用方**: `codex.rs`（Turn 执行中筛选连接器和工具）、`mcp_tool_call.rs`（获取 App 工具策略）、TUI/App Server（列出连接器）

## 实现备注

1. **内存缓存**：使用全局 `LazyLock<StdMutex<Option<CachedAccessibleConnectors>>>` 实现进程级缓存，TTL 由 `CONNECTORS_CACHE_TTL` 控制。
2. **启用状态层级**：user config `[apps]` > requirements > 默认启用。工具级别：tool_config.enabled > app.default_tools_enabled > annotations (destructive/open_world hints)。
3. **黑名单**：硬编码 `DISALLOWED_CONNECTOR_IDS` 和 `DISALLOWED_CONNECTOR_PREFIX`，第一方 Chat 客户端有额外黑名单。
4. **目录连接器发现**：通过 ChatGPT API (`/backend-api/aip/...`) 获取目录连接器，用于 tool_suggest 推荐未安装的连接器。
5. **合并策略**：`merge_connectors` 优先保留更详细的名称/描述，可访问连接器排在前面，按名称排序。
6. **Slug 生成**：`sanitize_slug` 将名称转为小写字母+数字+连字符，`sanitize_name` 额外将连字符替换为下划线（用于工具命名空间）。
