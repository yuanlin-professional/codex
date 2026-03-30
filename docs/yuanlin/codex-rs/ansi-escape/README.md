# ansi-escape

## 功能概述

`codex-ansi-escape` 是 Codex 项目的 ANSI 转义序列处理模块，负责将包含 ANSI 转义序列的文本字符串转换为 `ratatui` TUI 框架可渲染的 `Text` 和 `Line` 类型。它主要用于在终端 UI 中正确显示带有颜色和样式的命令输出，是 TUI 渲染管道中的关键文本处理层。

## 架构说明

该模块封装了 `ansi-to-tui` crate，提供两个核心转换函数和一个辅助的 Tab 展开功能：

### 处理流程

```
原始 ANSI 文本 -> expand_tabs() -> ansi-to-tui::IntoText -> ratatui::Text/Line
```

### Tab 展开

在转换前，模块会将制表符（`\t`）替换为 4 个空格。这是因为制表符与 TUI 的左侧边距前缀（如 `nl` 行号）交互时会产生视觉错位。使用固定宽度替换避免了需要维护跨 span 的制表位状态。

### 错误处理

`ansi_to_tui` 的解析错误（NomError、Utf8Error）被视为不可恢复错误，会触发 panic。在实际使用中，这些错误极为罕见。

## 目录结构

```
ansi-escape/
  Cargo.toml
  src/
    lib.rs   # 完整实现：ansi_escape、ansi_escape_line、expand_tabs
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `ansi-to-tui` | ANSI 转义序列到 ratatui 类型的核心转换 |
| `ratatui` | TUI 框架的 `Text` 和 `Line` 类型 |
| `tracing` | 多行警告日志 |

## 核心接口/API

### 函数

```rust
/// 将 ANSI 转义文本转换为 ratatui Text（多行）
pub fn ansi_escape(s: &str) -> Text<'static>;

/// 将 ANSI 转义文本转换为单个 ratatui Line
/// 如果输入包含多行，记录警告并仅返回第一行
pub fn ansi_escape_line(s: &str) -> Line<'static>;
```

### 使用场景

- 命令执行输出在 TUI 中的渲染
- 终端 transcript 模式下的文本显示
- 带颜色标记的日志信息展示
