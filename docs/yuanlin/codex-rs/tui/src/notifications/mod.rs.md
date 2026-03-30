# `mod.rs` — 桌面通知模块入口

## 功能概述

本模块提供桌面通知功能的统一接口，支持 OSC 9 和 BEL 两种通知后端。`DesktopNotificationBackend` 枚举封装两种后端，`detect_backend` 根据配置方法和环境变量自动选择合适的后端。Auto 模式下通过检测终端类型（iTerm、WezTerm、Ghostty、Kitty 等）决定使用 OSC 9 还是 BEL。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `DesktopNotificationBackend` | enum | 通知后端：Osc9 或 Bel |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `detect_backend` | `(method) -> DesktopNotificationBackend` | 根据配置检测并创建通知后端 |
| `supports_osc9` | `() -> bool` | 检测当前终端是否支持 OSC 9 |
| `notify` | `(&mut self, message) -> io::Result<()>` | 发送桌面通知 |

## 依赖关系

- **引用的 crate**: `codex_core::config::types::NotificationMethod`
- **被引用方**: TUI 应用在任务完成时发送通知

## 实现备注

- Windows Terminal (`WT_SESSION`) 不使用 OSC 9
- 通过 `TERM_PROGRAM`、`ITERM_SESSION_ID`、`TERM` 环境变量检测终端类型
