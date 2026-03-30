# `codex_tests_guardian.rs` — 测试模块

## 测试覆盖范围

Guardian 子代理集成测试，包括 shell 处理器与 Guardian 审批流程的交互、附加权限请求验证、统一 exec 处理器验证、压缩历史中 Guardian 开发者消息保留、粘性轮次权限、Guardian 子代理执行策略继承。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `guardian_allows_shell_additional_permissions_requests_past_policy_validation` | Guardian 允许 shell 附加权限请求通过策略验证 |
| `guardian_allows_unified_exec_additional_permissions_requests_past_policy_validation` | Guardian 允许统一 exec 附加权限请求通过策略验证 |
| `process_compacted_history_preserves_separate_guardian_developer_message` | 压缩历史处理保留独立的 Guardian 开发者消息 |
| `shell_handler_allows_sticky_turn_permissions_without_inline_request_permissions_feature` | shell 处理器在无内联请求权限特性时允许粘性轮次权限 |
| `guardian_subagent_does_not_inherit_parent_exec_policy_rules` | Guardian 子代理不继承父执行策略规则 |
