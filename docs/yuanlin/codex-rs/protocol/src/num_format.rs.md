# `num_format.rs` — 数字格式化工具

## 功能概述

提供面向用户的数字格式化功能，包括基于系统区域设置的千分位分隔符格式化（如 `12345` -> `12,345`）和 SI 后缀格式化（用于 token 计数显示，如 `1200` -> `1.20K`、`123456789` -> `123M`）。使用 ICU 国际化库实现本地化。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| 无公开结构体 | - | 仅导出两个格式化函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `format_with_separators` | `fn format_with_separators(n: i64) -> String` | 使用区域设置的千分位分隔符格式化整数 |
| `format_si_suffix` | `fn format_si_suffix(n: i64) -> String` | 以 3 位有效数字和 SI 后缀（K/M/G）格式化 token 计数 |

## 依赖关系

- **引用的 crate**: `icu_decimal`, `icu_locale_core`, `sys_locale`
- **被引用方**: `protocol.rs` 中 token 用量显示

## 实现备注

- 使用 `OnceLock` 缓存 `DecimalFormatter` 实例，首次访问时尝试使用系统区域设置，失败时回退到 en-US。
- SI 后缀格式化保持 3 位有效数字：`1.20K`（4位以上）、`10.0K`（5位）、`100K`（6位），超过 1000G 则保留整数 G 精度。
