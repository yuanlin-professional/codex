# `undo.rs` — 撤销任务

## 功能概述

实现了 `UndoTask`，负责将 Git 仓库恢复到最近一次 ghost snapshot 快照对应的状态。它从对话历史中查找最后一个 `GhostSnapshot` 条目，在阻塞线程中执行 `restore_ghost_commit_with_options` 恢复文件状态，成功后从历史中移除对应的快照条目。整个过程通过 `UndoStarted` 和 `UndoCompleted` 事件向客户端报告进度。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UndoTask` | 结构体 | 撤销任务，实现 `SessionTask` trait，`TaskKind` 为 `Regular` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn() -> Self` | 创建新的撤销任务 |
| `run` | `async fn(self: Arc<Self>, session, ctx, _input, cancellation_token) -> Option<String>` | 发射 `UndoStarted` → 查找最新 ghost snapshot → 阻塞恢复 → 更新历史 → 发射 `UndoCompleted` |

## 依赖关系

- **引用的 crate**: `async_trait`、`tokio`（spawn_blocking）、`tracing`、`codex_git_utils`（RestoreGhostCommitOptions、restore_ghost_commit_with_options）
- **引用的内部模块**: `crate::codex::TurnContext`、`crate::protocol`（EventMsg、UndoStartedEvent、UndoCompletedEvent）、`crate::state::TaskKind`
- **被引用方**: `tasks/mod.rs`（re-export 为 `UndoTask`）

## 实现备注

- 撤销操作在 `spawn_blocking` 中执行，因为 Git 操作是同步阻塞的。
- 成功恢复后会从对话历史中移除对应的 `GhostSnapshot` 条目，防止重复撤销。
- commit ID 在日志和用户消息中只显示前 7 位（short_id）。
- 若找不到 ghost snapshot，直接返回"无可用快照"的完成事件。
- 遥测计数器 `codex.task.undo` 记录撤销操作次数。
