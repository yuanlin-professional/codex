# `approvals.rs` — 测试模块

## 测试覆盖范围

测试命令执行审批(Approval)机制的各种场景，包括不同审批策略(Never/OnRequest/OnFailure)与沙箱策略(FullAccess/ReadOnly/WorkspaceWrite)的组合矩阵、apply_patch 审批、exec policy 修正(amendment)的持久化、子代理策略传播、zsh fork 前缀规则匹配、网络策略修正以及复合命令审批。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `approval_matrix_covers_all_modes` | 遍历所有审批策略与沙箱策略的组合矩阵，验证各模式下的审批行为 |
| `run_scenario` | 执行单个审批场景，包括 shell 命令、apply_patch 等不同工具类型 |
| `approving_apply_patch_for_session_skips_future_prompts_for_same_file` | 对某文件批准 apply_patch 后，同一会话中对该文件的后续操作跳过审批提示 |
| `approving_execpolicy_amendment_persists_policy_and_skips_future_prompts` | 批准 exec policy 修正后持久化策略，后续相同命令不再提示审批 |
| `spawned_subagent_execpolicy_amendment_propagates_to_parent_session` | 子代理中批准的 exec policy 修正传播到父会话 |
| `matched_prefix_rule_runs_unsandboxed_under_zsh_fork` | 匹配前缀规则的命令在 zsh fork 下无沙箱运行 |
| `invalid_requested_prefix_rule_falls_back_for_compound_command` | 复合命令的无效前缀规则回退到默认审批行为 |
| `approving_fallback_rule_for_compound_command_works` | 批准复合命令的回退规则后正常执行 |
| `denying_network_policy_amendment_persists_policy_and_skips_future_network_prompt` | 拒绝网络策略修正后持久化拒绝决定，后续跳过相同提示 |
| `compound_command_with_one_safe_command_still_requires_approval` | 复合命令中包含一个安全命令时仍需审批 |
