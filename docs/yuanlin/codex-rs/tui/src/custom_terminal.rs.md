# `custom_terminal.rs` — 自定义终端渲染引擎

## 功能概述

本文件是从 `ratatui::Terminal` 派生的自定义终端实现，提供了针对 Codex TUI 内联模式优化的双缓冲区渲染引擎。它实现了帧绘制、差异化刷新（只输出变化的单元格）、光标管理、视口调整、历史行计数以及多种清屏模式（视口、可见屏幕、滚动缓冲区、全部）。

差异化刷新算法对比前后两个缓冲区，生成最小化的绘制命令序列（Put 和 ClearToEnd），并优化了宽字符和 OSC 转义序列的宽度计算。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Terminal<B>` | 结构体 | 自定义终端，持有后端、双缓冲区、视口区域、光标状态、历史行计数 |
| `Frame<'a>` | 结构体 | 渲染帧，提供绘制区域和缓冲区访问 |
| `DrawCommand` | 枚举 | 绘制命令：Put（写入单元格）和 ClearToEnd（行尾清除） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `with_options` | `fn(backend) -> io::Result<Self>` | 创建终端实例 |
| `draw` | `fn(&mut self, callback) -> io::Result<()>` | 渲染单帧并刷新 |
| `flush` | `fn(&mut self) -> io::Result<()>` | 计算缓冲区差异并输出到后端 |
| `clear_scrollback` | `fn(&mut self) -> io::Result<()>` | 清除滚动缓冲区 |
| `clear_visible_screen` | `fn(&mut self) -> io::Result<()>` | 清除可见屏幕 |
| `note_history_rows_inserted` | `fn(&mut self, inserted_rows)` | 记录已插入的历史行数 |
| `diff_buffers` | `fn(a, b) -> Vec<DrawCommand>` | 比较两个缓冲区并生成差异绘制命令 |
| `display_width` | `fn(s: &str) -> usize` | 计算单元格符号的显示宽度（排除 OSC 转义序列） |

## 依赖关系

- **引用的 crate**: `ratatui`（Backend、Buffer、Style 类型）、`crossterm`（终端控制命令）、`unicode_width`
- **被引用方**: `tui.rs`（Tui 结构体持有 Terminal 实例）、`insert_history.rs`

## 实现备注

- 遵循 MIT 许可，标注了从 ratatui 源码的派生关系。
- ClearToEnd 优化：对每行扫描最右侧的非空白列，之后的空白区域用单个 ClearToEnd 命令替代多个空格写入。
- 宽字符处理：diff 算法追踪多宽度字符导致的无效化区域，确保替换宽字符时正确重绘相邻单元格。
- Modifier 差异计算复用了 ratatui 的 crossterm 属性映射。
