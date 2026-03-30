# `line_truncation.rs` — 行截断与省略号处理

## 功能概述

本文件提供带样式的 ratatui `Line` 的宽度计算和截断工具。支持按 Unicode 显示宽度精确截断（处理宽字符和零宽字符），以及在溢出时追加省略号的截断模式。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `line_width` | `fn(line) -> usize` | 计算行的 Unicode 显示宽度 |
| `truncate_line_to_width` | `fn(line, max_width) -> Line<'static>` | 将行精确截断到指定显示宽度 |
| `truncate_line_with_ellipsis_if_overflow` | `fn(line, max_width) -> Line<'static>` | 溢出时截断并追加省略号（`…`） |

## 依赖关系

- **引用的 crate**: `ratatui`（Line/Span）、`unicode_width`
- **被引用方**: UI 中需要截断显示的各种组件
