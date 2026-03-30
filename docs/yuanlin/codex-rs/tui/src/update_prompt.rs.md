# `update_prompt.rs` — 更新提示界面

## 功能概述

在 TUI 启动时检测到新版本后，显示一个全屏更新提示界面。
提供三个选项：立即更新、跳过、跳过直到下一版本。支持键盘导航和数字快速选择。
仅在 release 构建中编译（`#[cfg(not(debug_assertions))]`）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UpdatePromptOutcome` | enum | 提示结果：继续/执行更新 |
| `UpdateSelection` | enum | 用户选择：立即更新/跳过/不再提醒 |
| `UpdatePromptScreen` | struct | 更新提示界面状态 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_update_prompt_if_needed` | `async (tui, config) -> Result<Outcome>` | 按需运行更新提示 |

## 依赖关系

- **引用的模块**: `crate::update_action`, `crate::updates`, `crate::tui`, `crate::selection_list`, `crate::key_hint`

## 实现备注

- 选择"不再提醒"时通过 `updates::dismiss_version` 持久化到版本文件。
- 导航支持循环（从第一项按上到最后一项，反之亦然）。
