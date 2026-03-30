# `lib.rs` — ANSI 转义序列解析为 ratatui 文本

## 功能概述

提供将包含 ANSI 转义序列的字符串转换为 ratatui `Text` 和 `Line` 的工具函数。用于 TUI 和 CLI 的转录输出渲染。内含 tab 扩展功能，将制表符替换为 4 个空格以避免 TUI 中的视觉伪影。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ansi_escape_line` | `(s: &str) -> Line<'static>` | 解析单行 ANSI 文本，多行时取第一行并记录警告 |
| `ansi_escape` | `(s: &str) -> Text<'static>` | 解析完整的 ANSI 文本为 ratatui Text |
| `expand_tabs` | `(s: &str) -> Cow<str>` | 将制表符替换为 4 个空格 |

## 依赖关系

- **引用的 crate**: `ansi_to_tui`、`ratatui`、`tracing`
- **被引用方**: TUI 渲染组件
