# `tests.rs` — 测试模块

## 测试覆盖范围

测试 `truncate` 模块中的字符串分割和截断函数的正确性，包括基本分割、空字符串、仅前缀/后缀、重叠预算、UTF-8 边界处理、token 预算截断。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `split_string_works` | 基本字符串分割正确性 |
| `split_string_handles_empty_string` | 空字符串分割 |
| `split_string_only_keeps_prefix_when_tail_budget_is_zero` | 尾部预算为零时仅保留前缀 |
| `split_string_only_keeps_suffix_when_prefix_budget_is_zero` | 前缀预算为零时仅保留后缀 |
| `split_string_handles_overlapping_budgets_without_removal` | 重叠预算不丢字符 |
| `split_string_respects_utf8_boundaries` | UTF-8 多字节字符边界处理 |
| `truncate_with_token_budget_returns_original_when_under_limit` | token 预算充足返回原文 |
| `truncate_with_token_budget_reports_truncation_at_zero_limit` | 零预算报告截断 |
| `truncate_middle_tokens_handles_utf8_content` | token 截断正确处理 emoji |
| `truncate_middle_bytes_handles_utf8_content` | 字节截断正确处理 emoji |
