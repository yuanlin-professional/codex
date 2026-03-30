# `metadata_tests.rs` -- 测试模块

## 测试覆盖范围

覆盖 rollout 系统中会话元数据提取、回填（backfill）流程和 Git 信息合并逻辑。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `extract_metadata_from_rollout_uses_session_meta` | 从 rollout 文件中正确提取 session_meta 元数据 |
| `extract_metadata_from_rollout_returns_latest_memory_mode` | 多条 session_meta 时取最新的 memory_mode |
| `builder_from_items_falls_back_to_filename` | 无 session_meta 时从文件名推断 thread ID 和时间戳 |
| `backfill_sessions_resumes_from_watermark_and_marks_complete` | 回填从水位线恢复，跳过已处理文件，并标记完成 |
| `backfill_sessions_preserves_existing_git_branch_and_fills_missing_git_fields` | 回填保留已有的 git branch，补充缺失的 sha 和 origin_url |
| `backfill_sessions_normalizes_cwd_before_upsert` | 回填时对 cwd 进行规范化处理（如去除尾部 `.`） |
