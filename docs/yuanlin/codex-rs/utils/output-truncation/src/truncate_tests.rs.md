# `truncate_tests.rs` — 测试模块

## 测试覆盖范围

覆盖 `output-truncation` crate 中各种截断函数的正确性：字节/token 策略下的超限截断、未超限返回原文、行数统计报告、UTF-8 内容处理、多文本段截断与省略、内容项合并与图像保留。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `truncate_bytes_less_than_placeholder_returns_placeholder` | 字节预算极小时仍返回含占位符的截断结果 |
| `truncate_tokens_less_than_placeholder_returns_placeholder` | token 预算极小时仍返回含占位符的截断结果 |
| `truncate_tokens_under_limit_returns_original` | token 预算充足时返回原文 |
| `truncate_bytes_under_limit_returns_original` | 字节预算充足时返回原文 |
| `truncate_tokens_over_limit_returns_truncated` | token 预算不足时返回截断文本 |
| `truncate_bytes_over_limit_returns_truncated` | 字节预算不足时返回截断文本 |
| `truncate_bytes_reports_original_line_count_when_truncated` | 多行文本截断时报告原始行数 |
| `truncate_tokens_reports_original_line_count_when_truncated` | 多行文本 token 截断时报告原始行数 |
| `truncate_middle_bytes_handles_utf8_content` | 含 emoji 的 UTF-8 内容正确截断 |
| `truncates_across_multiple_under_limit_texts_and_reports_omitted` | 多文本段逐条截断并报告省略数 |
| `formatted_truncate_text_content_items_with_policy_returns_original_under_limit` | 总大小未超限时返回原始项 |
| `formatted_truncate_text_content_items_with_policy_preserves_empty_leading_text_behavior` | 空前导文本的边界处理 |
| `formatted_truncate_text_content_items_with_policy_merges_text_and_appends_images` | 合并文本段、截断并附加图像 |
| `formatted_truncate_text_content_items_with_policy_merges_all_text_for_token_budget` | token 预算下合并所有文本 |
| `byte_count_conversion_clamps_non_positive_values` | 非正字节数转换为 0 token |
