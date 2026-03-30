# codex_message_processor

## 功能概述

`codex_message_processor/` 子目录是 `CodexMessageProcessor` 的辅助模块集合，提供与应用列表（Apps）、插件（Plugin）和 MCP OAuth 认证相关的帮助函数。这些模块从主处理器文件 `codex_message_processor.rs` 中抽取出来，以保持代码组织的清晰度。

该目录的核心职责包括：
- 管理和查询可用的应用连接器（App Connectors）列表
- 处理插件所依赖的应用认证状态
- 自动执行 MCP 服务器的 OAuth 静默登录流程

## 目录结构

```
codex_message_processor/
├── apps_list_helpers.rs        # 应用列表查询与通知辅助函数
├── plugin_app_helpers.rs       # 插件应用认证状态辅助函数
└── plugin_mcp_oauth.rs         # MCP 服务器 OAuth 登录辅助
```

## 核心接口/API

### apps_list_helpers.rs - 应用列表辅助

- **`merge_loaded_apps(all_connectors, accessible_connectors) -> Vec<AppInfo>`**
  合并全量连接器列表和可访问连接器列表，生成最终的应用信息列表

- **`should_send_app_list_updated_notification(connectors, accessible_loaded, all_loaded) -> bool`**
  判断是否应该向客户端发送应用列表更新通知

- **`paginate_apps(connectors, start, limit) -> Result<AppsListResponse, JSONRPCErrorError>`**
  对应用列表进行分页处理，支持游标（cursor）翻页机制

- **`send_app_list_updated_notification(outgoing, data)`**
  通过出站消息发送器广播应用列表更新通知

### plugin_app_helpers.rs - 插件应用辅助

- **`load_plugin_app_summaries(config, plugin_apps) -> Vec<AppSummary>`**
  加载插件所关联的应用摘要信息，包括检查各应用的认证状态（`needs_auth` 字段）

- **`plugin_apps_needing_auth(all_connectors, accessible_connectors, plugin_apps, codex_apps_ready) -> Vec<AppSummary>`**
  从所有连接器中筛选出插件所需但尚未完成认证的应用列表

### plugin_mcp_oauth.rs - MCP OAuth 登录

- **`CodexMessageProcessor::start_plugin_mcp_oauth_logins(config, plugin_mcp_servers)`**
  为插件安装所需的 MCP 服务器批量发起 OAuth 静默登录。对每个服务器：
  1. 检测是否支持 OAuth 登录
  2. 解析 OAuth scope 配置
  3. 在后台任务中执行静默登录
  4. 如果首次因 scope 失败，自动重试无 scope 登录
  5. 完成后通过 `McpServerOauthLoginCompleted` 通知报告结果
