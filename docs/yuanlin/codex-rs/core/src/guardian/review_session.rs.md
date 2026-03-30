# `review_session.rs` — Guardian 审查 Session 管理与复用

## 功能概述

该模块管理 Guardian 审查 session 的生命周期和复用策略。它维护一个可复用的"主干"（trunk）session 和临时"分支"（ephemeral）session 池。当主干空闲时，后续审查直接追加到该 session 以保持稳定的 prompt-cache key；当主干正忙时，从最后提交的主干 rollout 分叉出临时 session 执行并行审查。主干在有效配置变更时被重建。Guardian session 被钉在只读沙箱中，禁用审批策略和非必要功能（spawn、collab、web search）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `GuardianReviewSessionManager` | struct | 审查 session 管理器，对外暴露 `run_review` 和 `shutdown` 接口 |
| `GuardianReviewSessionState` | struct (私有) | 管理器内部状态，持有主干 session 和临时 session 列表 |
| `GuardianReviewSession` | struct (私有) | 单个审查 session，含 `Codex` 实例、取消令牌、复用键、审查锁等 |
| `GuardianReviewSessionOutcome` | enum | session 运行结果：`Completed(Result<Option<String>>)`、`TimedOut`、`Aborted` |
| `GuardianReviewSessionParams` | struct | 审查运行参数，含父 session/turn、配置、提示词、schema、模型设置等 |
| `GuardianReviewSessionReuseKey` | struct (私有) | 复用键，包含影响 session 行为的所有配置字段，用于判断缓存是否需要失效 |
| `EphemeralReviewCleanup` | struct (私有) | RAII 守卫，确保临时 session 在 drop 时被清理 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_review` | `async fn(&self, params) -> GuardianReviewSessionOutcome` | 核心入口：获取或创建主干 session，主干忙时分叉临时 session |
| `shutdown` | `async fn(&self)` | 关闭主干和所有临时 session |
| `build_guardian_review_session_config` | `fn(parent_config, network, model, effort) -> Result<Config>` | 构建 Guardian session 配置：覆盖模型、禁用审批、设只读沙箱、禁用多 agent 功能 |
| `spawn_guardian_review_session` | `async fn(params, config, key, cancel, history) -> Result<GuardianReviewSession>` | 通过 `run_codex_thread_interactive` 启动 Guardian session |
| `run_review_on_session` | `async fn(session, params, deadline) -> (Outcome, bool)` | 在指定 session 上执行审查，返回结果和 session 是否可保留 |
| `wait_for_guardian_review` | `async fn(session, deadline, cancel) -> (Outcome, bool)` | 事件循环等待审查完成/超时/取消 |
| `interrupt_and_drain_turn` | `async fn(codex) -> Result<()>` | 中断当前 turn 并等待排空（最多 5 秒） |
| `run_before_review_deadline` | `async fn(deadline, cancel, future) -> Result<T, Outcome>` | 在截止时间前运行异步操作，超时或取消时返回对应结果 |

## 依赖关系

- **引用的 crate**: `codex_protocol`、`codex_features`、`codex_network_proxy`、`tokio`、`tokio_util`
- **引用的内部模块**: `crate::codex`、`crate::codex_delegate`、`crate::config`、`crate::rollout`
- **被引用方**: `guardian::review::run_guardian_review_session`

## 实现备注

- **复用键设计**: `GuardianReviewSessionReuseKey` 只包含影响 session 行为的字段（模型、provider、权限、指令、MCP 配置等），不依赖无关的配置簿记变更。
- **主干/分叉策略**: 主干 session 通过 `review_lock` 互斥锁保护，锁定时新请求从最后提交的 rollout 快照分叉。这确保并行审查不互相阻塞且不修改缓存的主干线程。
- **后续审查提醒**: 主干 session 的后续审查会注入提醒消息（`GUARDIAN_FOLLOWUP_REVIEW_REMINDER`），指导模型将先前审查作为上下文而非约束性先例。
- **配置锁定**: Guardian session 禁用 `SpawnCsv`、`Collab`、`WebSearchRequest`、`WebSearchCached` 功能，审批策略固定为 `Never`，沙箱固定为只读。如果这些功能被要求层强制开启，配置构建将失败。
- **超时处理**: 审查总超时 90 秒，超时后中断当前 turn 并最多等待 5 秒排空。
- **临时 session 清理**: `EphemeralReviewCleanup` RAII 守卫确保即使发生 panic，临时 session 也会被异步清理。
