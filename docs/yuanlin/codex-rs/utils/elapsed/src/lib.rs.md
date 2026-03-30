# `lib.rs` — 时间格式化工具

## 功能概述

提供时间格式化函数，将 `Instant` 或 `Duration` 转换为人类可读的紧凑字符串。格式规则：小于 1 秒显示毫秒（如 `250ms`），1-60 秒显示两位小数（如 `1.50s`），大于等于 60 秒显示分秒（如 `1m 15s`）。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `format_elapsed` | `fn(start_time: Instant) -> String` | 格式化自 start_time 以来的耗时 |
| `format_duration` | `fn(duration: Duration) -> String` | 格式化 Duration 为紧凑字符串 |

## 依赖关系

- **引用的 crate**: `std::time`
- **被引用方**: TUI 和日志系统中的耗时显示

## 实现备注

- 测试覆盖了亚秒、秒级和分钟级三个分支。
