# `format.rs` — 状态输出格式化工具

## 功能概述

提供 `/status` 输出中字段格式化的通用工具，包括 `FieldFormatter`（标签对齐和缩进管理）以及行宽计算、截断等辅助函数。`FieldFormatter` 根据所有标签的最大宽度自动计算值列的偏移量。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `FieldFormatter` | struct | 字段格式化器，管理缩进、标签宽度和值偏移 |

## 依赖关系

- **引用的 crate**: `ratatui`, `unicode_width`
- **被引用方**: `status::card`
