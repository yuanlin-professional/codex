# `thread_state.rs` — 线程级状态管理（监听器、Turn 历史、连接订阅）

## 功能概述

本模块实现了 `ThreadStateManager` 和 `ThreadState`，管理每个对话线程的运行时状态。`ThreadState` 跟踪线程监听器（事件消费任务）、当前 Turn 的历史记录构建器、待处理的中断/回滚请求、实验性原始事件标志等。`ThreadStateManager` 提供线程级的连接订阅管理，支持多连接订阅同一线程、连接断开时的清理、以及线程状态的创建和移除。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ThreadStateManager` | 结构体 | 全局线程状态管理器（线程 ↔ 连接的多对多关系） |
| `ThreadState` | 结构体 | 单个线程的运行时状态 |
| `TurnSummary` | 结构体 | Turn 摘要（已变更的文件、已执行的命令、最后错误） |
| `ThreadListenerCommand` | 枚举 | 线程监听器命令：`SendThreadResumeResponse`、`ResolveServerRequest` |
| `PendingThreadResumeRequest` | 结构体 | 待处理的线程恢复请求 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ThreadStateManager::thread_state` | `async fn(&self, thread_id) -> Arc<Mutex<ThreadState>>` | 获取或创建线程状态 |
| `ThreadStateManager::try_ensure_connection_subscribed` | `async fn(...) -> Option<Arc<Mutex<ThreadState>>>` | 确保连接订阅了指定线程 |
| `ThreadStateManager::remove_connection` | `async fn(&self, connection_id)` | 清理断开连接的所有订阅 |
| `ThreadState::set_listener` | `fn(&mut self, cancel_tx, conversation) -> (Receiver, generation)` | 设置事件监听器 |
| `ThreadState::clear_listener` | `fn(&mut self)` | 取消并清除监听器 |
| `ThreadState::track_current_turn_event` | `fn(&mut self, event)` | 将事件馈送到当前 Turn 的历史构建器 |

## 依赖关系

- **引用的 crate**: `codex_core`（`CodexThread`、`ThreadConfigSnapshot`）、`codex_app_server_protocol`（`ThreadHistoryBuilder`、`Turn`）、`codex_protocol`（`ThreadId`、`EventMsg`）
- **被引用方**: `codex_message_processor.rs`、`bespoke_event_handling.rs`

## 实现备注

- `listener_generation` 单调递增，用于防止过期的监听器处理事件。
- `ThreadListenerCommand` 通过 unbounded channel 发送到监听器任务，确保顺序性。
- 连接断开时保留线程监听器（不清除），因为其他连接可能仍在订阅。
