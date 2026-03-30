# `async_watcher_tests.rs` — 测试模块

## 测试覆盖范围

测试 `async_watcher.rs` 中的 `split_valid_utf8_prefix_with_max` 函数，验证 UTF-8 前缀分割在不同字节场景下的行为正确性。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `split_valid_utf8_prefix_respects_max_bytes_for_ascii` | 验证对 ASCII 数据按 `max_bytes` 限制正确切割，且缓冲区中保留剩余数据 |
| `split_valid_utf8_prefix_avoids_splitting_utf8_codepoints` | 验证不会在多字节 UTF-8 码点的中间位置切割（如 "e" 占 2 字节，max=3 只切出 1 个字符） |
| `split_valid_utf8_prefix_makes_progress_on_invalid_utf8` | 验证遇到无效 UTF-8 字节（0xff）时仍能逐字节输出，保证流持续推进 |
