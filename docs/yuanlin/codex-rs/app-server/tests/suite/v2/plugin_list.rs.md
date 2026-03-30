# `plugin_list.rs` — 测试模块

## 测试覆盖范围
全面测试 `plugin/list` JSON-RPC 方法，包括无效 marketplace 文件跳过、路径验证、
多 marketplace 加载、安装和启用状态、绝对资产路径、旧版提示格式、
远程同步错误处理、curated 插件状态协调、启动时远程同步以及精选插件 ID 获取和缓存。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `plugin_list_skips_invalid_marketplace_file_and_reports_error` | 跳过无效 marketplace 文件并报告错误 |
| `plugin_list_rejects_relative_cwds` | 拒绝相对路径的 cwd |
| `plugin_list_keeps_valid_marketplaces_when_another_marketplace_fails_to_load` | 某个 marketplace 加载失败不影响其他 |
| `plugin_list_accepts_omitted_cwds` | 省略 cwd 参数正常工作 |
| `plugin_list_includes_install_and_enabled_state_from_config` | 包含配置中的安装和启用状态 |
| `plugin_list_uses_home_config_for_enabled_state` | 使用 home 配置确定启用状态 |
| `plugin_list_returns_plugin_interface_with_absolute_asset_paths` | 返回包含绝对资产路径的插件接口 |
| `plugin_list_accepts_legacy_string_default_prompt` | 接受旧版字符串格式的默认提示 |
| `plugin_list_force_remote_sync_returns_remote_sync_error_on_fail_open` | 强制远程同步失败时返回错误 |
| `plugin_list_force_remote_sync_reconciles_curated_plugin_state` | 强制远程同步协调 curated 插件状态 |
| `app_server_startup_remote_plugin_sync_runs_once` | 启动时远程插件同步只运行一次 |
| `plugin_list_fetches_featured_plugin_ids_without_chatgpt_auth` | 无 ChatGPT 认证时也能获取精选插件 ID |
| `plugin_list_uses_warmed_featured_plugin_ids_cache_on_first_request` | 首次请求使用预热的精选插件缓存 |
