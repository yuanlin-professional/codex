# `line_utils.rs` — ratatui 行处理工具函数

## 功能概述

提供 ratatui `Line` 和 `Span` 的通用工具函数，包括借用行到拥有行的转换、行前缀添加和空行检测。这些工具被渲染管道中的多个模块共享使用。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `line_to_static` | `(line: &Line) -> Line<'static>` | 将借用的 Line 克隆为拥有的 'static Line |
| `push_owned_lines` | `(src, out)` | 将借用行的拥有副本追加到输出向量 |
| `is_blank_line_spaces_only` | `(line) -> bool` | 判断行是否为空或仅包含空格 |
| `prefix_lines` | `(lines, initial, subsequent) -> Vec<Line>` | 为每行添加前缀（首行和后续行可不同） |

## 依赖关系

- **引用的 crate**: `ratatui::text`
- **被引用方**: `exec_cell::render`、`streaming::controller`、`markdown_render` 等
