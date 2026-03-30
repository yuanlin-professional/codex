# src/notifications/

## 功能概述

`notifications/` 模块提供 TUI 的桌面通知后端。当 AI 助手完成任务或需要用户注意时，TUI 可以发送桌面通知。

支持两种通知后端：
- **OSC 9**：通过终端转义序列 `\x1b]9;message\x07` 发送通知，适用于 WezTerm、Ghostty、iTerm2 等支持 OSC 9 的终端
- **BEL**：通过标准响铃字符 `\x07` 发送通知，作为不支持 OSC 9 的终端的降级方案

自动检测逻辑会根据 `TERM_PROGRAM` 环境变量和终端类型选择最佳后端。

## 目录结构

```
notifications/
├── mod.rs    # 模块入口，后端枚举和自动检测逻辑
├── bel.rs    # BEL（响铃）通知后端
└── osc9.rs   # OSC 9 转义序列通知后端
```

## 核心接口/API

| 类型/函数 | 说明 |
|-----------|------|
| `DesktopNotificationBackend` | 通知后端枚举（Osc9/Bel） |
| `DesktopNotificationBackend::for_method()` | 根据配置的通知方法创建后端 |
| `DesktopNotificationBackend::notify()` | 发送桌面通知 |
| `detect_backend()` | 自动检测并创建最佳通知后端 |
| `BelBackend` | BEL 响铃通知后端 |
| `Osc9Backend` | OSC 9 通知后端 |
