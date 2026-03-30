# `session_startup_prewarm.rs` — 会话启动 WebSocket 预热

## 功能概述

此文件实现了 Codex 会话启动时的 WebSocket 连接预热机制。在用户开始第一个回合之前，系统会在后台预先建立 WebSocket 连接并准备好模型客户端会话，从而减少首次回合的等待时间。预热结果有三种状态：已就绪（Ready）、不可用（Unavailable）或已取消（Cancelled），并通过 OpenTelemetry 指标记录预热的耗时和状态。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SessionStartupPrewarmHandle` | 结构体 | 预热任务的句柄，包含 JoinHandle、启动时间和超时配置 |
| `SessionStartupPrewarmResolution` | 枚举 | 预热解析结果：`Ready`（已就绪）、`Unavailable`（不可用，含状态和耗时）、`Cancelled`（已取消） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `SessionStartupPrewarmHandle::resolve` | `async (self, telemetry, cancellation_token) -> Resolution` | 等待预热完成或超时/取消，记录遥测指标 |
| `Session::schedule_startup_prewarm` | `async (self: &Arc<Self>, base_instructions)` | 调度启动预热任务 |
| `Session::consume_startup_prewarm_for_regular_turn` | `async (&self, cancellation_token) -> Resolution` | 在首个回合中消费预热结果 |
| `schedule_startup_prewarm_inner` | `async (session, base_instructions) -> CodexResult<ModelClientSession>` | 预热的内部实现：构建工具和 prompt，调用 `prewarm_websocket` |

## 依赖关系

- **引用的 crate**: `codex_otel`（遥测指标）、`tokio`（异步任务和超时）、`tokio_util::sync::CancellationToken`
- **被引用方**: `Session` 在启动时调用 `schedule_startup_prewarm`，首个回合通过 `consume_startup_prewarm_for_regular_turn` 使用

## 实现备注

- 预热超时使用 WebSocket 连接超时配置（默认 15 秒）
- 预热任务完成后立即检查是否已结束，避免不必要的等待
- 超时或取消时通过 `task.abort()` 终止后台任务
- 遥测指标包括 `STARTUP_PREWARM_AGE_AT_FIRST_TURN_METRIC`（预热到首次回合的间隔）和 `STARTUP_PREWARM_DURATION_METRIC`（预热总耗时）
- 预热使用 `INITIAL_SUBMIT_ID` 作为回合 ID
