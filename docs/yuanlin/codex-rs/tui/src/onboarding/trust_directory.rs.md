# `trust_directory.rs` — 目录信任确认组件

## 功能概述

本模块实现引导流程中的目录信任确认步骤，提示用户是否信任当前工作目录的内容。用户可以选择"是，继续"或"否，退出"。选择信任后会将项目配置为 Trusted 级别。该组件同时支持 Windows 沙箱创建提示。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TrustDirectoryWidget` | struct | 目录信任 UI 组件，包含 cwd、选择状态和高亮项 |
| `TrustDirectorySelection` | enum | 用户选择：Trust（信任）或 Quit（退出） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle_trust` | `(&mut self)` | 处理信任选择，调用 `set_project_trust_level` |
| `handle_quit` | `(&mut self)` | 处理退出选择 |
| `handle_key_event` | `(&mut self, key_event)` | 键盘导航（j/k/1/2/y/n/Enter） |

## 依赖关系

- **引用的 crate**: `codex_core::config`, `codex_git_utils`, `codex_protocol::config_types`
- **被引用方**: `OnboardingScreen` 信任步骤

## 实现备注

- 信任目标优先使用 git 项目根目录（通过 `resolve_root_git_project_for_trust`）
- Release 类型的按键事件会被忽略，只处理 Press/Repeat
- 设置信任失败时显示错误信息但不阻塞流程
