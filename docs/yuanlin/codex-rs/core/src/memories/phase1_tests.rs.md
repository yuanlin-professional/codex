# `phase1_tests.rs` -- 测试模块

## 测试覆盖范围

覆盖 Phase 1 的 rollout 条目序列化过滤逻辑和作业结果统计聚合函数。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `serializes_memory_rollout_with_agents_removed_but_environment_kept` | 验证序列化时移除 AGENTS.md/skill 消息但保留环境上下文和 subagent 通知 |
| `count_outcomes_sums_token_usage_across_all_jobs` | 验证 `aggregate_stats` 正确累加多个作业的 token 使用量 |
| `count_outcomes_keeps_usage_empty_when_no_job_reports_it` | 验证当所有作业均无 token 使用量时，聚合结果为 None |
