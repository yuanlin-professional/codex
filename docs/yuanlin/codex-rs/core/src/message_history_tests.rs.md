# `message_history_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖消息历史记录的读写功能，包括历史条目的查找（lookup）、追加后使用稳定 log_id 进行查询、以及历史文件超过最大字节限制时的修剪（trim）行为，验证硬限制和软限制（soft cap）的裁剪策略。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `lookup_reads_history_entries` | 验证从历史文件中按偏移量正确读取条目 |
| `lookup_uses_stable_log_id_after_appends` | 验证追加新条目后，使用初始 log_id 仍能正确查询后续条目 |
| `append_entry_trims_history_when_beyond_max_bytes` | 验证追加条目导致超过最大字节限制时正确淘汰旧条目 |
| `append_entry_trims_history_to_soft_cap` | 验证修剪策略满足软限制比率（soft cap ratio），进行比硬限制更激进的裁剪 |
