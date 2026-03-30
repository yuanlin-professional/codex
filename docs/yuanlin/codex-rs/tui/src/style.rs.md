# `style.rs` — TUI 全局样式辅助

## 功能概述

提供 TUI 中用户消息和建议计划的背景色样式函数。根据终端检测到的背景色（浅色/深色主题），
通过颜色混合生成适当的背景色，使用户消息在视觉上与助手消息区分开。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `user_message_style` | `() -> Style` | 用户消息背景样式 |
| `proposed_plan_style` | `() -> Style` | 建议计划背景样式（与用户消息相同） |
| `user_message_bg` | `(terminal_bg) -> Color` | 计算用户消息背景色 |

## 依赖关系

- **引用的模块**: `crate::color`, `crate::terminal_palette`
