# `recorder_tests.rs` -- 测试模块

## 测试覆盖范围

覆盖 RolloutRecorder 的延迟物化（deferred materialization）、事件持久化、state DB 更新、线程列表分页/过滤、以及会话恢复候选匹配逻辑。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `recorder_materializes_only_after_explicit_persist` | rollout 文件仅在显式调用 `persist()` 后才创建，缓冲事件保持顺序 |
| `metadata_irrelevant_events_touch_state_db_updated_at` | 非元数据事件（如 agent_message）仅更新 updated_at 而不改变 title 等字段 |
| `metadata_irrelevant_events_fall_back_to_upsert_when_thread_missing` | 当 state DB 中不存在 thread 时，非元数据事件回退到 upsert 操作 |
| `list_threads_db_disabled_does_not_skip_paginated_items` | DB 未启用时列表分页不跳过项目，正确支持游标翻页 |
| `list_threads_db_enabled_drops_missing_rollout_paths` | DB 启用时自动清除指向不存在 rollout 文件的记录 |
| `list_threads_db_enabled_repairs_stale_rollout_paths` | DB 启用时自动修复过期的 rollout 路径为正确路径 |
| `resume_candidate_matches_cwd_reads_latest_turn_context` | 恢复候选匹配使用最新 TurnContext 中的 cwd 进行比较 |
