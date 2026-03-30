# `lib.rs` — 字符串工具集

## 功能概述

提供多种字符串处理工具函数。`take_bytes_at_char_boundary` 和 `take_last_bytes_at_char_boundary` 在字节预算内安全截取 UTF-8 字符串的前缀和后缀。`sanitize_metric_tag_value` 将度量标签值中的非法字符替换为 `_`。`find_uuids` 使用正则提取字符串中的所有 UUID。`normalize_markdown_hash_location_suffix` 将 `#L74C3` 格式转换为 `:74:3` 格式。还重新导出 `truncate` 模块中的截断函数。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `take_bytes_at_char_boundary` | `fn(s, maxb) -> &str` | 字节预算内截取前缀 |
| `take_last_bytes_at_char_boundary` | `fn(s, maxb) -> &str` | 字节预算内截取后缀 |
| `sanitize_metric_tag_value` | `fn(value) -> String` | 清理度量标签值 |
| `find_uuids` | `fn(s) -> Vec<String>` | 查找所有 UUID |
| `normalize_markdown_hash_location_suffix` | `fn(suffix) -> Option<String>` | 转换 markdown 位置后缀 |

## 依赖关系

- **引用的 crate**: `regex_lite`
- **被引用方**: `output-truncation`, 度量收集, 文件引用解析

## 实现备注

- UUID 正则使用 `OnceLock` 惰性初始化。
- 度量标签值最大 256 字符，全非字母数字时返回 `"unspecified"`。
