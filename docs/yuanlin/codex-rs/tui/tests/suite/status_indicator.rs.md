# `status_indicator.rs` — 测试模块

## 测试覆盖范围

回归测试：验证 `StatusIndicatorWidget` 对 ANSI 转义序列的清理功能，确保 `ansi_escape_line()` 函数能正确剥离原始的 `\x1b` 转义字节，只保留可打印的字形内容。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `ansi_escape_line_strips_escape_sequences` | 对包含 ANSI 红色着色序列 `\x1b[31mRED\x1b[0m` 的字符串调用 `ansi_escape_line()`，验证返回的 Line 中各 span 组合后仅包含 "RED" 三个可打印字符，不含任何原始转义字节。 |
