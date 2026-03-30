# `job_control.rs` — 作业控制（挂起/恢复）

## 功能概述

本模块协调 TUI 的挂起（Ctrl+Z / SIGTSTP）和恢复处理，确保终端上下文在挂起后能正确恢复。在挂起时记录恢复路径（重新对齐 inline viewport 或恢复 alt screen），缓存 inline 光标行位置，并在恢复后消费挂起意图执行相应的视口调整。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SuspendContext` | struct | 挂起上下文（Clone，Arc/atomic 内部实现） |
| `ResumeAction` | enum | 恢复动作：RealignInline 或 RestoreAlt |
| `PreparedResumeAction` | enum | 预备恢复动作：RestoreAltScreen 或 RealignViewport |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `suspend` | `(&self, alt_screen_active) -> Result<()>` | 执行挂起：保存状态 + 发送 SIGTSTP |
| `prepare_resume_action` | `(&self, terminal, viewport) -> Option<PreparedResumeAction>` | 消费挂起意图，预计算视口变更 |
| `set_cursor_y` | `(&self, value)` | 更新缓存的光标行位置 |

## 依赖关系

- **引用的 crate**: `crossterm`, `ratatui`, `libc`
- **被引用方**: TUI 事件循环中的 Ctrl+Z 处理

## 实现备注

- 挂起时如果 alt screen 活跃，先退出 alt screen 再发送 SIGTSTP
- 恢复后通过 `set_modes()` 重新应用终端模式
- `SUSPEND_KEY` 定义为 Ctrl+Z
- 使用 `Arc<Mutex>` 和 `Arc<AtomicU16>` 实现跨任务共享
