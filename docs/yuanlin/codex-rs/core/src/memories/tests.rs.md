# `tests.rs` -- 记忆子系统集成测试

## 测试覆盖范围

覆盖记忆子系统的核心功能，包括路径计算、Phase 1 输出 schema 验证、`clear_memory_root_contents` 安全清理逻辑、rollout 摘要同步、raw_memories.md 重建，以及 Phase 2 合并流程的端到端集成测试（使用真实状态数据库和线程管理器）。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `memory_root_uses_shared_global_path` | 验证 `memory_root` 返回 `codex_home/memories` 路径 |
| `stage_one_output_schema_requires_rollout_slug_and_keeps_it_nullable` | 验证 Phase 1 JSON Schema 包含可空的 `rollout_slug` 字段 |
| `clear_memory_root_contents_preserves_root_directory` | 验证清空操作保留根目录本身 |
| `clear_memory_root_contents_rejects_symlinked_root` | (Unix) 验证拒绝清理符号链接指向的目录 |
| `sync_rollout_summaries_and_raw_memories_file_keeps_latest_memories_only` | 验证同步操作保留最新记忆、删除过期文件 |
| `sync_rollout_summaries_uses_timestamp_hash_and_sanitized_slug_filename` | 验证摘要文件名格式和 slug 清理 |
| `rebuild_raw_memories_file_adds_canonical_rollout_summary_file_header` | 验证 raw_memories.md 中的元数据头和内容格式 |
| `completion_watermark_never_regresses_below_claimed_input_watermark` | 验证完成水位标记不低于声明水位 |
| `completion_watermark_uses_claimed_watermark_when_there_are_no_memories` | 验证无记忆时使用声明水位 |
| `completion_watermark_uses_latest_memory_timestamp_when_it_is_newer` | 验证最新记忆时间戳大于声明水位时采用较大值 |
| `dispatch_skips_when_global_job_is_not_dirty` | 验证无脏数据时跳过 Phase 2 |
| `dispatch_skips_when_global_job_is_already_running` | 验证作业已运行时跳过 Phase 2 |
| `dispatch_reclaims_stale_global_lock_and_starts_consolidation` | 验证回收过期锁并启动合并子代理 |
| `dispatch_with_empty_stage1_outputs_rebuilds_local_artifacts` | 验证无输入时重建空产物并清理旧文件 |
| `dispatch_marks_job_for_retry_when_sandbox_policy_cannot_be_overridden` | 验证沙箱策略冲突时标记重试 |
| `dispatch_marks_job_for_retry_when_syncing_artifacts_fails` | 验证文件同步失败时标记重试 |
| `dispatch_marks_job_for_retry_when_rebuilding_raw_memories_fails` | 验证 raw_memories 重建失败时标记重试 |
| `dispatch_marks_job_for_retry_when_spawn_agent_fails` | 验证子代理启动失败时标记重试 |
