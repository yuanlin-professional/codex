# `terminal_title.rs` — 终端标题输出管理

## 功能概述

管理终端窗口标题的设置和清除。对不可信输入文本（模型输出、线程名、路径等）
进行安全净化：移除控制字符、bidi/不可见格式字符、合并冗余空白，并截断到
240 字符上限。通过 OSC 0 转义序列写入标题。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SetTerminalTitleResult` | enum | 标题设置结果：已应用/无可见内容 |
| `SetWindowTitle` | struct | crossterm Command 实现，写入 OSC 0 序列 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `set_terminal_title` | `(title) -> Result<SetTerminalTitleResult>` | 设置终端标题 |
| `clear_terminal_title` | `() -> Result<()>` | 清除终端标题 |
| `sanitize_terminal_title` | `(title) -> String` | 净化标题文本 |

## 依赖关系

- **引用的 crate**: `crossterm`, `ratatui`

## 实现备注

- OSC 序列使用 ST（ESC \）终止符而非 BEL，遵循 xterm/ctlseqs 规范。
- 净化过程去除 Trojan Source 相关的 bidi 控制字符（U+202A-U+202E 等）。
- 限制为 240 字符以适应大多数终端标签栏的显示宽度。
