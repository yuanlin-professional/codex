# `head_tail_buffer_tests.rs` — 测试模块

## 测试覆盖范围

测试 `HeadTailBuffer` 的核心功能，包括头尾保留、容量为零时的行为、排空重置、超大数据块处理以及跨多个小块的渐进填充。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `keeps_prefix_and_suffix_when_over_budget` | 验证超出容量时保留头部前缀和尾部后缀，中间部分被丢弃 |
| `max_bytes_zero_drops_everything` | 验证容量为 0 时所有数据被丢弃，`retained_bytes` 为 0，`omitted_bytes` 正确累计 |
| `head_budget_zero_keeps_only_last_byte_in_tail` | 验证容量为 1 时（头部预算为 0），只保留最后 1 个字节 |
| `draining_resets_state` | 验证 `drain_chunks` 正确返回所有数据块并重置缓冲区状态 |
| `chunk_larger_than_tail_budget_keeps_only_tail_end` | 验证单个数据块超过尾部预算时，只保留其末尾部分 |
| `fills_head_then_tail_across_multiple_chunks` | 验证通过多个小数据块渐进填充头部和尾部，以及超出后尾部淘汰最旧数据 |
