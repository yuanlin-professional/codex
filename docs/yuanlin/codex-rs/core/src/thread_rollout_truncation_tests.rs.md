# `thread_rollout_truncation_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖线程推演历史（thread rollout）的截断功能，包括从起始位置按用户消息计数截断、usize::MAX 时保留完整推演、线程回滚标记（ThreadRolledBack）对截断位置的影响，以及会话前缀消息不被计入用户消息计数。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `truncates_rollout_from_start_before_nth_user_only` | 验证按第 N 个用户消息从起始截断，仅保留截断点之前的项（包含 assistant、reasoning 和 tool call 项的混合场景） |
| `truncation_max_keeps_full_rollout` | 验证 n_from_start 为 usize::MAX 时保留完整推演历史 |
| `truncates_rollout_from_start_applies_thread_rollback_markers` | 验证线程回滚标记正确影响有效用户消息计数，使截断位置符合回滚后的逻辑顺序 |
| `ignores_session_prefix_messages_when_truncating_rollout_from_start` | 验证会话初始上下文中的前缀消息不被计入用户消息截断计数 |
