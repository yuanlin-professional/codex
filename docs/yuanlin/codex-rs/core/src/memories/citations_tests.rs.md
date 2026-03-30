# `citations_tests.rs` -- 测试模块

## 测试覆盖范围

覆盖 `citations.rs` 中的 `parse_memory_citation` 和 `get_thread_id_from_citations` 函数，验证 XML 标签解析、引用条目提取、rollout ID 去重以及新旧标签格式兼容性。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `get_thread_id_from_citations_extracts_thread_ids` | 验证从 `<thread_ids>` 标签中提取有效 ThreadId，跳过无效 UUID |
| `get_thread_id_from_citations_supports_legacy_rollout_ids` | 验证兼容旧版 `<rollout_ids>` 标签格式的 ThreadId 提取 |
| `parse_memory_citation_extracts_entries_and_rollout_ids` | 验证同时提取引用条目（路径、行范围、注释）和去重后的 rollout ID |
