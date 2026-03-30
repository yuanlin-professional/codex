# `thread_unsubscribe.rs` — 测试模块

## 测试覆盖范围
测试 `thread/unsubscribe` JSON-RPC 方法，验证取消订阅卸载线程并发送 thread/closed 通知、
turn 运行中取消订阅自动中断 turn、取消订阅清除缓存状态以便恢复，以及卸载后报告未加载状态。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_unsubscribe_unloads_thread_and_emits_thread_closed_notification` | 取消订阅卸载线程并发送关闭通知 |
| `thread_unsubscribe_during_turn_interrupts_turn_and_emits_thread_closed` | turn 运行中取消订阅自动中断 turn |
| `thread_unsubscribe_clears_cached_status_before_resume` | 取消订阅清除缓存状态 |
| `thread_unsubscribe_reports_not_loaded_after_thread_is_unloaded` | 卸载后报告未加载状态 |
