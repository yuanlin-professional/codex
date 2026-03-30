# `resume_picker.rs` — 会话恢复选择器

## 功能概述

实现 `/resume` 命令的会话恢复选择器界面。支持从本地线程存储和远程 App Server
加载历史会话列表，以分页方式展示。用户可通过上下键导航、Enter 选择、搜索过滤
等方式选择要恢复的会话。支持无限滚动加载和近底部自动预加载。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SessionTarget` | struct | 恢复目标（路径 + 线程 ID） |
| `ThreadItem` / `ThreadsPage` | 外部类型 | 线程列表项和分页数据 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_resume_picker` | `async (...) -> Option<SessionTarget>` | 运行恢复选择器事件循环 |

## 依赖关系

- **引用的 crate**: `codex_core`, `codex_app_server_protocol`, `codex_protocol`, `chrono`, `crossterm`, `ratatui`
- **引用的模块**: `crate::app_server_session`, `crate::tui`, `crate::key_hint`, `crate::text_formatting`

## 实现备注

- 分页大小为 25 条，接近底部 5 条时自动加载下一页。
- 同时支持本地存储（`codex_core` 的线程列表）和远程 App Server（通过 gRPC）。
- 显示线程名称、工作目录、最后修改时间等信息。
