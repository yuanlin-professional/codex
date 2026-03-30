# `thread_status.rs` — 测试模块

## 测试覆盖范围
测试 `thread/status/changed` 通知的运行时更新发送和 opt-out 机制。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_status_changed_emits_runtime_updates` | 线程状态变更时发送运行时更新通知 |
| `thread_status_changed_can_be_opted_out` | 可通过 opt-out 屏蔽状态变更通知 |
