# `lib.rs` — 输出截断工具

## 功能概述

提供基于 `TruncationPolicy`（字节或 token 预算）的输出截断辅助函数。支持纯文本截断、带行数统计的格式化截断，以及对 `FunctionCallOutputContentItem` 列表的截断处理（合并文本段并保留图像项）。token 估算使用约 4 字节/token 的近似计算。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `formatted_truncate_text` | `fn(content, policy) -> String` | 带总行数前缀的格式化截断 |
| `truncate_text` | `fn(content, policy) -> String` | 纯文本截断 |
| `formatted_truncate_text_content_items_with_policy` | `fn(items, policy) -> (Vec<...>, Option<usize>)` | 合并文本段后截断，保留图像 |
| `truncate_function_output_items_with_policy` | `fn(items, policy) -> Vec<...>` | 按条目逐一截断，统计省略数 |
| `approx_tokens_from_byte_count_i64` | `fn(bytes: i64) -> i64` | 字节数到 token 数的近似转换 |

## 依赖关系

- **引用的 crate**: `codex_protocol`, `codex_utils_string`
- **被引用方**: 命令执行结果处理模块

## 实现备注

- 截断标记格式为 `…N chars truncated…` 或 `…N tokens truncated…`。
- 超限文本项显示 `[omitted N text items ...]` 摘要。
