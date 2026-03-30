# `plugin_install.rs` — 测试模块

## 测试覆盖范围
测试 `plugin/install` JSON-RPC 方法，涵盖路径验证、缺失/不可用/不允许的插件拒绝、
远程同步强制安装、分析事件跟踪、需要认证的应用返回，以及捆绑 MCP 服务器的后续可用性。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `plugin_install_rejects_relative_marketplace_paths` | 拒绝相对路径的 marketplace |
| `plugin_install_returns_invalid_request_for_missing_marketplace_file` | 缺失的 marketplace 文件返回错误 |
| `plugin_install_returns_invalid_request_for_not_available_plugin` | 不可用的插件返回错误 |
| `plugin_install_returns_invalid_request_for_disallowed_product_plugin` | 不允许的产品插件返回错误 |
| `plugin_install_force_remote_sync_enables_remote_plugin_before_local_install` | 强制远程同步后本地安装 |
| `plugin_install_tracks_analytics_event` | 安装操作跟踪分析事件 |
| `plugin_install_returns_apps_needing_auth` | 返回需要认证的应用列表 |
| `plugin_install_filters_disallowed_apps_needing_auth` | 过滤不允许的需认证应用 |
| `plugin_install_makes_bundled_mcp_servers_available_to_followup_requests` | 安装后捆绑的 MCP 服务器可用 |
