# `sandboxing_tests.rs` — 测试模块

## 测试覆盖范围
验证默认审批需求计算和首次尝试沙箱覆盖逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `external_sandbox_skips_exec_approval_on_request` | ExternalSandbox 策略下 OnRequest 跳过审批 |
| `restricted_sandbox_requires_exec_approval_on_request` | ReadOnly 策略下 OnRequest 需要审批 |
| `default_exec_approval_requirement_rejects_sandbox_prompt_when_granular_disables_it` | Granular 中 sandbox_approval=false 时返回 Forbidden |
| `default_exec_approval_requirement_keeps_prompt_when_granular_allows_sandbox_approval` | Granular 中 sandbox_approval=true 时保留 NeedsApproval |
| `additional_permissions_allow_bypass_sandbox_first_attempt_when_execpolicy_skips` | Skip+bypass_sandbox=true 时首次跳过沙箱 |
| `guardian_bypasses_sandbox_for_explicit_escalation_on_first_attempt` | RequireEscalated 权限首次跳过沙箱 |
