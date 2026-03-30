# `welcome.rs` — 欢迎页组件

## 功能概述

本模块实现引导流程中的欢迎页面，显示 ASCII 动画和 "Welcome to Codex" 文字。动画仅在终端尺寸足够（宽度>=60、高度>=37）且启用动画时显示。支持 Ctrl+. 快捷键切换动画变体。已登录用户的欢迎步骤状态为 Hidden。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `WelcomeWidget` | struct | 欢迎页组件，包含登录状态、ASCII 动画和布局区域 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `(is_logged_in, request_frame, animations_enabled) -> Self` | 创建欢迎组件 |
| `update_layout_area` | `(&self, area)` | 更新用于尺寸判断的布局区域 |
| `get_step_state` | `(&self) -> StepState` | 已登录返回 Hidden，否则返回 Complete |

## 依赖关系

- **引用的 crate**: `ratatui`, `crate::ascii_animation`
- **被引用方**: `OnboardingScreen` 欢迎步骤

## 实现备注

- 动画最小显示尺寸：60x37
- Ctrl+. 通过 `AsciiAnimation::pick_random_variant` 切换变体
