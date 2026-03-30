# `key_hint.rs` — 按键绑定提示与匹配

## 功能概述

本文件定义了 `KeyBinding` 结构体和一组便捷构造函数，用于在 TUI 的状态栏和提示文本中显示按键绑定提示（如 "ctrl + c"、"⌥ + e"）。它也提供了按键事件匹配功能和修饰键检测工具。macOS 上 Alt 键显示为 `⌥`，其他平台显示为 `alt`。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `KeyBinding` | 结构体 | 按键绑定：key + modifiers |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `plain` | `fn(key) -> KeyBinding` | 无修饰键绑定 |
| `alt` | `fn(key) -> KeyBinding` | Alt 修饰键绑定 |
| `ctrl` | `fn(key) -> KeyBinding` | Ctrl 修饰键绑定 |
| `ctrl_alt` | `fn(key) -> KeyBinding` | Ctrl+Alt 修饰键绑定 |
| `is_press` | `fn(&self, event) -> bool` | 检查按键事件是否匹配此绑定 |
| `has_ctrl_or_alt` | `fn(mods) -> bool` | 检查修饰键是否包含 Ctrl 或 Alt（排除 AltGr） |

## 依赖关系

- **引用的 crate**: `crossterm::event`（键盘事件类型）、`ratatui`（Span 样式）
- **被引用方**: 几乎所有处理键盘事件的 TUI 模块
