# `onboarding_screen.rs` — 引导屏幕编排器

## 功能概述

本模块是 TUI 引导（onboarding）流程的主编排器，按步骤顺序管理 Welcome（欢迎）、Auth（认证）和 TrustDirectory（目录信任）三个阶段。它处理键盘事件分发、步骤状态机推进、app-server 事件响应，以及渲染时的动态布局计算。`run_onboarding_app` 是异步主循环，驱动整个引导流程直到完成或退出。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `OnboardingScreen` | struct | 引导屏幕主体，包含步骤列表和完成/退出标志 |
| `Step` | enum | 引导步骤：Welcome、Auth、TrustDirectory |
| `StepState` | enum | 步骤状态：Hidden、InProgress、Complete |
| `OnboardingResult` | struct | 引导结果，包含目录信任决策和是否退出 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_onboarding_app` | `async (args, app_server, tui) -> Result<OnboardingResult>` | 引导流程的异步主循环 |
| `is_done` | `(&self) -> bool` | 判断引导是否完成 |
| `handle_key_event` | `(&mut self, key_event)` | 分发键盘事件到当前活跃步骤 |

## 依赖关系

- **引用的 crate**: `codex_app_server_client`, `codex_core::config`, `ratatui`
- **被引用方**: `main` 函数在启动时调用

## 实现备注

- 步骤按顺序执行，当前步骤完成后自动进入下一步
- Ctrl+C/Ctrl+D/q 退出（API Key 输入模式下 q 不退出）
- ChatGPT 登录成功后会清除 SGR 属性并重绘以避免样式残留
- 渲染使用临时 Buffer 测量每个步骤的实际高度，实现自适应布局
