# `tui.rs` — TUI 终端管理核心

## 功能概述

TUI 的终端生命周期管理核心模块。负责终端初始化（raw mode、bracketed paste、
键盘增强标志、焦点变化检测）、恢复（退出时还原终端状态）、事件流管理、
备用屏幕切换、内联视口管理、帧调度、桌面通知以及外部程序协作（暂时恢复终端状态
以运行外部交互程序）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Tui` | struct | TUI 终端管理器，持有终端、事件代理、帧调度器等状态 |
| `TuiEvent` | enum | TUI 事件：Key/Paste/Draw |
| `FrameRequester` | struct（re-export） | 帧调度请求器 |
| `RestoreMode` | enum | 终端恢复模式：完全恢复/保持 raw mode |
| `EnableAlternateScroll` / `DisableAlternateScroll` | struct | 备用滚动模式控制命令 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `init` | `() -> Result<Terminal>` | 初始化终端（内联视口） |
| `set_modes` | `() -> Result<()>` | 设置终端模式 |
| `restore/restore_keep_raw` | `() -> Result<()>` | 恢复终端状态 |
| `Tui::new` | `(terminal) -> Self` | 创建 TUI 管理器 |
| `Tui::draw` | `(&mut self, height, draw_fn) -> Result<()>` | 同步更新绘制 |
| `Tui::with_restored` | `async (&mut self, mode, f) -> R` | 暂时恢复终端运行外部程序 |
| `Tui::enter/leave_alt_screen` | `(&mut self) -> Result<()>` | 进入/离开备用屏幕 |
| `Tui::event_stream` | `(&self) -> Pin<Box<dyn Stream<Item = TuiEvent>>>` | 获取事件流 |
| `Tui::notify` | `(&mut self, message) -> bool` | 发送桌面通知 |

## 依赖关系

- **引用的 crate**: `crossterm`, `ratatui`, `tokio`
- **引用的模块**: `crate::custom_terminal`, `crate::notifications`, `crate::tui::event_stream`, `crate::tui::frame_requester`, `crate::tui::job_control`（Unix）

## 实现备注

- 使用 `SynchronizedUpdate` 将所有绘制操作包裹在同步更新中，减少撕裂。
- 支持 Zellij 等复用器通过 `set_alt_screen_enabled(false)` 禁用备用屏幕。
- Unix 平台支持 Ctrl+Z 挂起/恢复（通过 `SuspendContext`）。
- 视口调整会在终端大小变化时保持光标位置稳定。
- 设置 panic hook 确保崩溃时恢复终端状态。
