# `app_list.rs` — 测试模块

## 测试覆盖范围
测试 `app/list` JSON-RPC 方法的完整行为，包括连接器（connectors）的列表获取、
分页、缓存机制、强制刷新、启用/禁用状态、异步加载通知更新、API Key 认证下的行为、
线程级特性标志覆盖，以及实验性特性切换触发的列表刷新。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `list_apps_returns_empty_when_connectors_disabled` | 连接器功能禁用时返回空列表 |
| `list_apps_returns_empty_with_api_key_auth` | API Key 认证模式下返回空列表 |
| `list_apps_uses_thread_feature_flag_when_thread_id_is_provided` | 指定 thread_id 时使用线程级特性标志 |
| `list_apps_reports_is_enabled_from_config` | 配置中的 enabled 状态反映在结果中 |
| `list_apps_emits_updates_and_returns_after_both_lists_load` | 两个数据源加载完成后发送更新通知并返回合并结果 |
| `list_apps_waits_for_accessible_data_before_emitting_directory_updates` | 等待可访问数据加载后才发送目录更新 |
| `list_apps_does_not_emit_empty_interim_updates` | 不发送空的中间更新通知 |
| `list_apps_paginates_results` | 分页查询正常工作 |
| `list_apps_force_refetch_preserves_previous_cache_on_failure` | 强制刷新失败时保留旧缓存 |
| `list_apps_force_refetch_patches_updates_from_cached_snapshots` | 强制刷新时基于缓存快照补丁更新 |
| `experimental_feature_enablement_set_refreshes_apps_list_when_apps_turn_on` | 启用应用特性标志后自动刷新列表 |
