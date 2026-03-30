# `review.rs` — Guardian 审查流程与决策逻辑

## 功能概述

该模块实现了 Guardian 审查的主流程，负责协调审批请求的提交、审查 session 的运行、评估结果的解析和最终审批决策的生成。它始终采用"关闭失败"策略：超时、session 故障或解析失败均视为高风险拒绝。审查完成后向父 session 发送 `GuardianAssessment` 事件和 `Warning` 事件用于追踪和用户通知。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `GuardianReviewOutcome` | enum (crate 内部) | 审查结果：`Completed(Result<GuardianAssessment>)`、`TimedOut`、`Aborted` |
| `GUARDIAN_REJECTION_MESSAGE` | const | 拒绝消息，指示 agent 不应尝试变通或间接执行被拒绝的操作 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `review_approval_request` | `async fn(session, turn, request, retry_reason) -> ReviewDecision` | 公开的审批请求审查入口 |
| `review_approval_request_with_cancel` | `async fn(session, turn, request, retry_reason, cancel_token) -> ReviewDecision` | 支持外部取消的审批请求审查入口 |
| `routes_approval_to_guardian` | `fn(turn) -> bool` | 判断当前 turn 是否应将审批路由到 Guardian（需 `on-request` 策略且审查者为 `GuardianSubagent`） |
| `is_guardian_reviewer_source` | `fn(session_source) -> bool` | 判断 session 来源是否为 Guardian 审查者 |
| `run_guardian_review` | `async fn(...) -> ReviewDecision` | 内部核心审查流程，协调提示词构建、session 运行和结果处理 |
| `run_guardian_review_session` | `async fn(session, turn, prompt_items, schema, cancel) -> GuardianReviewOutcome` | 在受限的复用 session 中运行 Guardian 审查 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`ReviewDecision`、`GuardianAssessmentEvent` 等）、`tokio_util`（`CancellationToken`）
- **引用的内部模块**: `super::prompt`、`super::review_session`、`super::approval_request`、`crate::codex`
- **被引用方**: 审批处理流程中的 `on-request` 路径

## 实现备注

- **关闭失败策略**: `run_guardian_review` 对所有非正常路径都生成 `risk_score: 100` 的拒绝评估，确保安全默认行为。
- **事件流**: 审查开始时发送 `InProgress` 状态事件，完成时发送 `Approved/Denied` 或 `Aborted` 状态事件，并通过 `Warning` 事件向用户展示评估结果。
- **风险阈值**: `risk_score < 80` 自动批准，>= 80 拒绝。
- **取消支持**: 通过 `CancellationToken` 支持外部取消，取消后立即发送 `Aborted` 事件并返回 `ReviewDecision::Abort`。
- **模型选择**: 优先使用 `GUARDIAN_PREFERRED_MODEL`（gpt-5.4），不可用时回退到父 turn 的当前模型，并尽量使用 `Low` 推理努力度。
- **网络继承**: Guardian session 继承父 session 的网络代理配置用于只读检查，但不继承执行策略规则。
