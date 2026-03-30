# `composer_input.rs` — 公共可复用文本输入组件

## 功能概述

本模块提供内部 `ChatComposer` 的公共包装器，供其他 crate（如 codex-cloud-tasks）复用成熟的 composer 行为：多行输入、粘贴启发式、Enter 提交、Shift+Enter 换行。它通过一个内部通道丢弃 `AppEvent` 以保持独立运行。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ComposerInput` | struct | 公共文本输入组件，包装内部 ChatComposer |
| `ComposerAction` | enum | 输入动作：Submitted（用户提交文本）或 None |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `input` | `(&mut self, key) -> ComposerAction` | 处理键盘事件，返回高级动作 |
| `handle_paste` | `(&mut self, pasted) -> bool` | 处理粘贴事件 |
| `set_hint_items` | `(&mut self, items)` | 设置底部提示项 |
| `desired_height` | `(&self, width) -> u16` | 计算给定宽度下的所需高度 |
| `cursor_pos` | `(&self, area) -> Option<(u16, u16)>` | 计算光标位置 |
| `is_in_paste_burst` | `(&self) -> bool` | 检查粘贴突发检测是否活跃 |

## 依赖关系

- **引用的 crate**: `crate::bottom_pane::ChatComposer`, `crate::render::renderable`
- **被引用方**: `codex-cloud-tasks` 等外部 crate

## 实现备注

- 内部创建一个不使用的 `AppEventSender` 通道来满足 ChatComposer 的依赖
- `drain_app_events` 在每次操作后清空内部通道
- 默认占位文本为 "Compose new task"
