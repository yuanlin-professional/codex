# `storage_tests.rs` -- 测试模块

## 测试覆盖范围

覆盖 `storage.rs` 中的 rollout 摘要文件名生成函数，验证 UUID 时间戳提取、短哈希计算和 slug 截断/清理逻辑。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `rollout_summary_file_stem_uses_uuid_timestamp_and_hash_when_slug_missing` | 验证无 slug 时文件名仅包含时间戳和短哈希 |
| `rollout_summary_file_stem_sanitizes_and_truncates_slug` | 验证 slug 被截断到 60 字符，特殊字符被替换为下划线，转为小写 |
| `rollout_summary_file_stem_uses_uuid_timestamp_and_hash_when_slug_is_empty` | 验证空字符串 slug 等同于无 slug |
