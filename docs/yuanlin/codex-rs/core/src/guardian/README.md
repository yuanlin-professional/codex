# guardian/ -- 守卫审批

## 功能概述

`guardian/` 实现了 Codex 的自动化审批守卫系统（Guardian Review）。当工具执行需要用户审批（`on-request` 策略）时，Guardian 可以自动评估操作的风险等级，对低风险操作自动批准，避免频繁打断用户。它通过构建精简的对话转录，调用专用的 Guardian 审查会话来评估风险，并基于风险分数阈值（默认 < 80）决定是否自动批准。系统在超时、执行失败或输出格式错误时采用"失败即拒绝"策略。

## 目录结构

```
guardian/
├── mod.rs                # 模块入口，定义 GuardianAssessment/GuardianEvidence、常量和导出
├── approval_request.rs   # GuardianApprovalRequest：审批请求构建和 JSON 序列化
├── prompt.rs             # Guardian 审查提示词构建
├── review.rs             # 核心审查逻辑：routes_approval_to_guardian()、review_approval_request()
├── review_session.rs     # GuardianReviewSessionManager：审查会话管理
├── policy.md             # Guardian 策略文档
├── snapshots/            # 测试快照
└── tests.rs              # 测试
```

## 依赖关系

- **上游依赖**：`codex-protocol`（`GuardianRiskLevel`、`ReviewDecision`）
- **内部依赖**：`codex/`（Session、TurnContext）、`config/`（Config）、`tools/sandboxing`（审批上下文）
- **被依赖方**：`tools/orchestrator.rs`（编排器在审批流程中调用 Guardian）、`tools/network_approval.rs`（网络审批中检查 Guardian 路由）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `GuardianAssessment` | 评估结果：risk_level、risk_score (0-100)、rationale、evidence 列表 |
| `GuardianEvidence` | 评估证据项：message 和 why |
| `GuardianApprovalRequest` | 审批请求，封装待审查的操作上下文 |
| `routes_approval_to_guardian()` | 判断某个审批请求是否应路由到 Guardian |
| `review_approval_request()` | 执行 Guardian 审查并返回 ReviewDecision |
| `GuardianReviewSessionManager` | Guardian 审查会话管理器 |
| `GUARDIAN_REJECTION_MESSAGE` | Guardian 拒绝时返回给模型的消息 |
| `GUARDIAN_APPROVAL_RISK_THRESHOLD` | 风险分数阈值（80），低于此值自动批准 |
