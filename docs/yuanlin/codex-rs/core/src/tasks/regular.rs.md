# `regular.rs` — 常规对话任务

## 功能概述

实现了 `RegularTask`，这是最基本也是最常用的会话任务类型，驱动标准的用户-AI 对话交互。它发射 `TurnStarted` 事件后调用 `run_turn` 执行实际的模型推理循环，并在有待处理输入时自动继续下一轮。还会在首轮时尝试消费会话启动预热（prewarm）的客户端会话。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RegularTask` | 结构体 | 常规对话任务，实现 `SessionTask` trait，`TaskKind` 为 `Regular` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn() -> Self` | 创建新的常规任务 |
| `kind` | `fn(&self) -> TaskKind` | 返回 `TaskKind::Regular` |
| `run` | `async fn(self: Arc<Self>, session, ctx, input, cancellation_token) -> Option<String>` | 发射 `TurnStarted` → 消费 prewarm → 循环调用 `run_turn` 直到无待处理输入 |

## 依赖关系

- **引用的 crate**: `async_trait`、`tokio_util`、`tracing`
- **引用的内部模块**: `crate::codex::run_turn`/`TurnContext`、`crate::protocol`（EventMsg、TurnStartedEvent）、`crate::session_startup_prewarm`、`crate::state::TaskKind`
- **被引用方**: `tasks/mod.rs`（re-export 为 `RegularTask`）、`Session::ensure_task_for_queued_response_items`

## 实现备注

- 使用循环机制处理一轮结束后产生的新输入（如 hook 注入的 pending input），避免启动新任务。
- 首轮会尝试消费启动预热的客户端会话，取消时直接返回 `None`。
- `TurnStarted` 事件在任务内部发射，而非等待预热完成后，以减少用户感知的延迟。
