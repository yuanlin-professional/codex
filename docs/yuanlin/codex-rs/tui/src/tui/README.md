# src/tui/

## 功能概述

`tui/` 子目录包含 TUI 事件循环和终端管理的底层基础设施模块。这些模块为主应用提供事件流合并、帧率控制、帧调度和作业控制（挂起/恢复）能力。

核心组件：
- **EventBroker / TuiEventStream**：共享 crossterm 输入流，支持暂停/恢复（用于让出 stdin 给外部编辑器等场景），并将 crossterm 事件映射为 `TuiEvent`
- **FrameRequester / FrameScheduler**：基于 Actor 模式的帧调度系统，合并多个重绘请求为单次通知，避免不必要的重绘
- **FrameRateLimiter**：将帧率限制在 120 FPS（约 8.33ms 间隔），避免浪费计算资源
- **SuspendContext**（仅 Unix）：协调 SIGTSTP 挂起和恢复，正确处理备用屏幕和内联视口的切换

## 目录结构

```
tui/
├── event_stream.rs       # EventBroker + TuiEventStream（事件源抽象和合并）
├── frame_rate_limiter.rs  # FrameRateLimiter（帧率限制器，120 FPS）
├── frame_requester.rs     # FrameRequester + FrameScheduler（帧调度 Actor）
└── job_control.rs         # SuspendContext（Unix 挂起/恢复处理）
```

## 核心接口/API

| 类型/函数 | 说明 |
|-----------|------|
| `EventBroker` | 共享 crossterm 输入状态，支持暂停/恢复底层事件源 |
| `TuiEventStream` | 合并 crossterm 事件和帧绘制通知的统一事件流 |
| `EventSource` trait | 事件源抽象，支持测试时替换为 `FakeEventSource` |
| `CrosstermEventSource` | 生产环境的 crossterm 事件源 |
| `FrameRequester` | 帧调度请求句柄，可跨任务克隆共享 |
| `FrameRequester::schedule_frame()` | 请求尽快重绘 |
| `FrameRequester::schedule_frame_in(dur)` | 请求在指定延迟后重绘 |
| `FrameRateLimiter` | 帧率限制器，clamp 重绘截止时间到最小间隔 |
| `MIN_FRAME_INTERVAL` | 最小帧间隔常量（约 8.33ms，120 FPS） |
| `SuspendContext`（Unix） | 挂起/恢复上下文，记录恢复路径和光标位置 |
| `SUSPEND_KEY` | 挂起快捷键（Ctrl+Z） |
