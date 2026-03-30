# `compaction.rs` — 测试模块

## 测试覆盖范围
测试端到端的上下文压缩（compaction）流程，包括自动本地压缩、自动远程压缩、
手动触发压缩，以及无效/未知线程 ID 的拒绝处理。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `auto_compaction_local_emits_started_and_completed_items` | 本地自动压缩触发 item/started 和 item/completed 通知 |
| `auto_compaction_remote_emits_started_and_completed_items` | 远程自动压缩正确调用 compact 端点并发送通知 |
| `thread_compact_start_triggers_compaction_and_returns_empty_response` | 手动触发压缩返回空响应并发送通知 |
| `thread_compact_start_rejects_invalid_thread_id` | 无效线程 ID 被拒绝 |
| `thread_compact_start_rejects_unknown_thread_id` | 未知线程 ID 被拒绝 |
