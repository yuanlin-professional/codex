# `plugin_uninstall.rs` — 测试模块

## 测试覆盖范围
测试 `plugin/uninstall` JSON-RPC 方法，验证插件缓存和配置条目的删除、
远程同步卸载以及分析事件跟踪。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `plugin_uninstall_removes_plugin_cache_and_config_entry` | 卸载删除缓存和配置条目 |
| `plugin_uninstall_force_remote_sync_calls_remote_uninstall_first` | 强制远程同步时先调用远程卸载 |
| `plugin_uninstall_tracks_analytics_event` | 卸载操作跟踪分析事件 |
