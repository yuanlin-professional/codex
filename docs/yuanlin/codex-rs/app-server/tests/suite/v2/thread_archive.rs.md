# `thread_archive.rs` — 测试模块

## 测试覆盖范围
测试 `thread/archive` JSON-RPC 方法，验证归档需要已物化的 rollout 文件，
以及归档前清除过期订阅以确保恢复正常。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_archive_requires_materialized_rollout` | 未物化的线程归档被拒绝，物化后成功 |
| `thread_archive_clears_stale_subscriptions_before_resume` | 归档前清除过期订阅，恢复后正常工作 |
