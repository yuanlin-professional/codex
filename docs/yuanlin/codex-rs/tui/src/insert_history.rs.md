# `insert_history.rs` — 历史行插入与终端滚动

## 功能概述

本文件实现了在视口上方插入历史行的底层终端操作。它将 ratatui 的 `Line` 列表写入终端滚动缓冲区，处理自适应换行（URL 行保持不换行以便终端点击）、ANSI 滚动区域管理（DECSTBM）、以及行样式到 span 的合并渲染。

这是 TUI 内联模式的关键组件，使得对话历史能够像传统终端输出一样向上滚动，而视口固定在底部。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SetScrollRegion` | 结构体 | 设置终端滚动区域的 ANSI 命令 |
| `ResetScrollRegion` | 结构体 | 重置终端滚动区域的 ANSI 命令 |
| `ModifierDiff` | 结构体 | 计算样式修饰符差异并生成对应 ANSI 属性命令 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `insert_history_lines` | `fn(terminal, lines) -> io::Result<()>` | 在视口上方插入历史行，处理换行、滚动和样式渲染 |
| `write_spans` | `fn(writer, spans) -> io::Result<()>` | 将带样式的 span 序列写入终端输出流 |

## 依赖关系

- **引用的 crate**: `crossterm`（终端命令）、`ratatui`（Line/Span 类型）、`crate::wrapping`（自适应换行）、`crate::custom_terminal`
- **被引用方**: `tui.rs`（Tui::insert_history_lines 方法）

## 实现备注

- URL 行不进行硬换行，交给终端自行字符换行，以保持链接的可点击性。
- 混合行（URL + 普通文字）使用自适应换行：URL token 保持完整，非 URL 文字正常换行。
- 多物理行的 URL 在插入前预清除续行内容，避免残留旧内容。
- 行级样式通过 `patch` 合并到每个 span，确保 blockquote 等整行着色在续行中保持一致。
- 包含大量 VT100 后端测试验证颜色、换行和滚动行为。
