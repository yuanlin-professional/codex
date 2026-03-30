# `lib.rs` — 大小写不敏感的模糊匹配

## 功能概述

提供简单的大小写不敏感子序列模糊匹配函数。`fuzzy_match` 函数在 haystack 中查找 needle 的字符子序列，返回匹配字符的原始索引和评分（越小越好）。匹配在小写副本上执行但维护到原始字符索引的映射，以支持 Unicode 小写展开（如 `ß` -> `ss`, `İ` -> `i̇`）。前缀匹配获得 -100 的奖励分。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `fuzzy_match` | `fn(haystack: &str, needle: &str) -> Option<(Vec<usize>, i32)>` | 模糊匹配，返回索引和评分 |
| `fuzzy_indices` | `fn(haystack: &str, needle: &str) -> Option<Vec<usize>>` | 便捷封装，仅返回匹配索引 |

## 依赖关系

- **引用的 crate**: 无外部依赖
- **被引用方**: TUI 中的过滤/搜索 UI

## 实现备注

- 空 needle 匹配所有内容，返回空索引和 `i32::MAX` 评分。
- 评分 = (最后匹配位置 - 第一个匹配位置 + 1 - needle 长度)，前缀匹配减 100。
- 测试涵盖 ASCII、Unicode（Turkish İ、German ß）、连续性偏好和前缀奖励。
