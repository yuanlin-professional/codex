# `thread_status.rs` — 线程状态追踪与 thread/status/changed 通知发布

## 功能概述

本模块实现了 `ThreadWatchManager`，追踪所有活跃线程的运行时状态并在状态变化时向客户端发送 `thread/status/changed` 通知。线程状态由 `RuntimeFacts` 驱动，包含加载状态、Turn 运行状态、待处理的权限/用户输入请求计数、系统错误标志，最终映射为 `ThreadStatus` 枚举（`NotLoaded`、`Idle`、`Active`、`SystemError`）。权限请求和用户输入请求通过 RAII `ThreadWatchActiveGuard` 自动追踪。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ThreadWatchManager` | 结构体 | 线程状态追踪管理器，支持通知发布和运行中 Turn 计数订阅 |
| `ThreadWatchActiveGuard` | 结构体（RAII） | 待处理请求的生命周期 guard，drop 时自动递减计数 |
| `RuntimeFacts` | 结构体 | 线程运行时事实：is_loaded、running、pending 计数、has_system_error |
| `ThreadWatchActiveGuardType` | 枚举 | `Permission` 或 `UserInput` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `upsert_thread` | `async fn(&self, thread)` | 注册/更新线程并发送通知 |
| `remove_thread` | `async fn(&self, thread_id)` | 移除线程并发送 NotLoaded 通知 |
| `note_turn_started` | `async fn(&self, thread_id)` | 记录 Turn 开始 |
| `note_turn_completed` | `async fn(&self, thread_id, failed)` | 记录 Turn 完成 |
| `note_permission_requested` | `async fn(&self, thread_id) -> ThreadWatchActiveGuard` | 记录权限请求（返回 RAII guard） |
| `note_user_input_requested` | `async fn(&self, thread_id) -> ThreadWatchActiveGuard` | 记录用户输入请求（返回 RAII guard） |
| `subscribe_running_turn_count` | `fn(&self) -> watch::Receiver<usize>` | 订阅运行中 Turn 计数（用于优雅重启） |
| `resolve_thread_status` | `fn(status, has_in_progress_turn) -> ThreadStatus` | 解析最终状态（in-progress turn 覆盖 Idle/NotLoaded） |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`（`ThreadStatus`、`ThreadStatusChangedNotification`）、`tokio::sync::watch`
- **被引用方**: `codex_message_processor.rs`、`bespoke_event_handling.rs`、`lib.rs`（优雅重启依赖 Turn 计数）

## 实现备注

- 状态变更仅在状态实际改变时才发送通知（避免重复推送）。
- `upsert_thread_silently` 不发送通知，用于 thread/resume 等需要静默注册的场景。
- `ThreadWatchActiveGuard` 的 `Drop` 实现使用 `tokio::runtime::Handle::spawn` 异步更新计数。
- 包含全面的单元测试覆盖状态转换、通知发布、guard 生命周期、运行 Turn 计数等。
