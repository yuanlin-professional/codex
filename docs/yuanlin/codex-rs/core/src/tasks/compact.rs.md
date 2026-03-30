# `compact.rs` — 会话压缩任务

## 功能概述

实现了 `CompactTask`，这是一个会话任务，负责执行对话历史的压缩操作。它根据当前 provider 的配置，选择使用远程压缩服务（`run_remote_compact_task`）或本地压缩逻辑（`run_compact_task`），并通过遥测计数器记录执行类型。压缩任务用于在对话历史过长时进行摘要/精简，以减少上下文窗口占用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CompactTask` | 结构体 | 压缩任务，实现 `SessionTask` trait，`TaskKind` 为 `Compact` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `kind` | `fn(&self) -> TaskKind` | 返回 `TaskKind::Compact` |
| `run` | `async fn(self: Arc<Self>, session, ctx, input, cancellation_token) -> Option<String>` | 执行压缩：根据 provider 选择远程或本地压缩路径，并记录遥测数据 |

## 依赖关系

- **引用的 crate**: `async_trait`、`tokio_util`
- **引用的内部模块**: `super::SessionTask`/`SessionTaskContext`、`crate::codex::TurnContext`、`crate::state::TaskKind`、`crate::compact`/`crate::compact_remote`
- **被引用方**: `tasks/mod.rs`（re-export 为 `CompactTask`）
