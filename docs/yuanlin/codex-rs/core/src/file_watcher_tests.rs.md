# `file_watcher_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖文件监视器（FileWatcher）系统的核心功能，包括节流接收器（ThrottledWatchReceiver）的合并与刷新行为、变更事件类型过滤（仅传递变更性事件）、路径注册与取消注册、递归/非递归监视模式、订阅者通知路由、以及事件循环的过滤逻辑。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `throttled_receiver_coalesces_within_interval` | 验证节流接收器在时间间隔内合并多个路径变更事件 |
| `throttled_receiver_flushes_pending_on_shutdown` | 验证发送端关闭时节流接收器刷新待处理事件后正确终止 |
| `is_mutating_event_filters_non_mutating_event_kinds` | 验证 Create 和 Modify 为变更事件，Access 为非变更事件被过滤 |
| `register_dedupes_by_path_and_scope` | 验证路径注册按路径和递归范围去重计数 |
| `watch_registration_drop_unregisters_paths` | 验证注册句柄 drop 后自动取消路径监视 |
| `subscriber_drop_unregisters_paths` | 验证订阅者 drop 后自动取消其所有路径监视 |
| `receiver_closes_when_subscriber_drops` | 验证订阅者 drop 后接收端正确关闭 |
| `recursive_registration_downgrades_to_non_recursive_after_drop` | 验证递归注册 drop 后降级为非递归监视模式 |
| `unregister_holds_state_lock_until_unwatch_finishes` | 验证取消注册操作持有状态锁直到底层 unwatch 完成，防止竞态条件 |
| `matching_subscribers_are_notified` | 验证文件变更仅通知匹配路径的订阅者 |
| `non_recursive_watch_ignores_grandchildren` | 验证非递归监视模式忽略嵌套子目录中的变更 |
| `ancestor_events_notify_child_watches` | 验证祖先路径事件能通知监视其子路径的订阅者 |
| `spawn_event_loop_filters_non_mutating_events` | 验证事件循环过滤非变更事件（Access），仅传递变更事件（Create） |
