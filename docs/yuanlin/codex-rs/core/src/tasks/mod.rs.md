# `mod.rs` — 会话任务系统入口与生命周期管理

## 功能概述

本文件是 `tasks` 模块的入口文件，定义了会话任务（SessionTask）trait 及其完整的生命周期管理机制。它负责任务的创建、启动、中断、完成后的指标收集和事件发射。通过 `Session` 上的方法（`spawn_task`、`abort_all_tasks`、`on_task_finished`）统一管理所有类型的任务（常规对话、Review、压缩、Ghost 快照、撤销、用户 Shell 命令）。同时提供中断标记、turn 级别的 token 使用量统计和网络代理指标采集。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SessionTask` | trait | 会话任务接口，定义 `kind`（任务类型）、`span_name`（追踪名称）、`run`（执行）和 `abort`（中止清理） |
| `SessionTaskContext` | 结构体 | 任务执行时对 `Session` 的薄封装，暴露 `clone_session`、`auth_manager`、`models_manager` 方法 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `spawn_task` | `async fn<T: SessionTask>(self: &Arc<Self>, turn_context, input, task)` | 中止当前所有任务后启动新任务 |
| `start_task` | `async fn<T: SessionTask>(self: &Arc<Self>, turn_context, input, task)` | 内部方法：包装任务为 Arc、启动 Tokio 后台任务、设置 `ActiveTurn` 和追踪 span |
| `abort_all_tasks` | `async fn(self: &Arc<Self>, reason: TurnAbortReason)` | 取消所有运行中的任务，处理中断清理，清除待处理审批 |
| `on_task_finished` | `async fn(self: &Arc<Self>, turn_context, last_agent_message)` | 任务完成回调：记录待处理输入、发射 token 使用量指标、发送 `TurnComplete` 事件 |
| `handle_task_abort` | `async fn(self: &Arc<Self>, task: RunningTask, reason)` | 处理单个任务的中止：取消令牌 → 等待优雅停止 → abort 句柄 → 中断标记写入历史 |
| `interrupted_turn_history_marker` | `fn() -> ResponseItem` | 生成中断标记消息，告知模型上一轮被用户中断 |
| `emit_turn_network_proxy_metric` | `fn(session_telemetry, network_proxy_active, tmp_mem)` | 发射网络代理活动状态的遥测计数器 |
| `ensure_task_for_queued_response_items` | `async fn(self: &Arc<Self>)` | 若有待处理的响应项且无活跃任务，自动启动 `RegularTask` |
| `close_unified_exec_processes` | `async fn(&self)` | 终止所有统一执行进程 |
| `cleanup_after_interrupt` | `async fn(&self, turn_context)` | 中断后清理：中止 JS REPL 内核等 |

## 依赖关系

- **引用的 crate**: `async_trait`、`tokio`、`tokio_util`、`tracing`、`codex_otel`（遥测指标）、`codex_protocol`（协议类型）、`codex_features`
- **引用的内部模块**: `crate::codex::Session`/`TurnContext`、`crate::hook_runtime`（输入钩子）、`crate::models_manager`、`crate::protocol`（事件类型）、`crate::state`（ActiveTurn、RunningTask、TaskKind）
- **子模块**: `compact`、`ghost_snapshot`、`regular`、`review`、`undo`、`user_shell`
- **被引用方**: `Session` 的对话执行流程直接调用

## 实现备注

- 优雅中断超时为 100ms（`GRACEFULL_INTERRUPTION_TIMEOUT_MS`），超时后强制 abort 任务句柄。
- 中断时会向对话历史写入一个特殊的标记消息，提示模型上一轮被中断，工具可能部分执行。
- token 使用量按差值计算：turn 结束时的总量减去 turn 开始时的总量。
- 使用 `AbortOnDropHandle` 确保任务句柄在 Drop 时自动 abort，防止泄露。
- 任务完成后会检查是否有排队的响应项需要处理，通过 `PendingInputHookDisposition` 决定接受或阻止。
