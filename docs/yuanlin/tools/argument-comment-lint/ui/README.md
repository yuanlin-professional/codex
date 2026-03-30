# argument-comment-lint/ui - UI 测试用例

## 功能概述

包含参数注释 lint 工具的 UI 测试文件，用于验证 lint 规则的输出是否正确。

## 文件列表

| 文件 | 说明 |
|------|------|
| `allow_char_literals.rs` | 允许字符字面量的测试 |
| `allow_string_literals.rs` | 允许字符串字面量的测试 |
| `comment_matches_multiline.rs` | 多行注释匹配测试 |
| `comment_matches.rs` | 注释匹配测试 |
| `comment_mismatch.rs` | 注释不匹配测试 |
| `comment_mismatch.stderr` | 预期错误输出 |
| `ignore_external_methods.rs` | 忽略外部方法测试 |
| `uncommented_literal.rs` | 未注释字面量测试 |
| `uncommented_literal.stderr` | 预期错误输出 |
