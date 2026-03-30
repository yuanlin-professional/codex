# `truncate.rs` — 字符串中间截断工具

## 功能概述

提供大段输出的中间截断功能，保留前缀和后缀并在 UTF-8 字符边界上切割。`truncate_middle_chars` 基于字节预算截断并插入字符计数标记（`…N chars truncated…`），`truncate_middle_with_token_budget` 基于 token 预算截断并插入 token 计数标记。使用约 4 字节/token 的近似计算。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `truncate_middle_chars` | `fn(s, max_bytes) -> String` | 基于字节预算的中间截断 |
| `truncate_middle_with_token_budget` | `fn(s, max_tokens) -> (String, Option<u64>)` | 基于 token 预算的中间截断 |
| `approx_token_count` | `fn(text) -> usize` | 近似 token 计数 |
| `approx_bytes_for_tokens` | `fn(tokens) -> usize` | token 数转字节数 |
| `approx_tokens_from_byte_count` | `fn(bytes) -> u64` | 字节数转 token 数 |

## 依赖关系

- **引用的 crate**: 无外部依赖
- **被引用方**: `output-truncation` crate

## 实现备注

- 预算按 50/50 分配给前缀和后缀。
- `split_string` 在 UTF-8 字符边界上安全切割，避免截断多字节字符。
