# `unix_escalation_tests.rs` — 测试模块

## 测试覆盖范围
验证 Unix 权限提升模块的策略评估、命令解析、审批拒绝逻辑以及 execve 拦截策略的各种场景。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `execve_prompt_rejection_keeps_prefix_rules_on_rules_flag` | Granular 策略中 `rules=false` 时拒绝前缀规则匹配的审批 |
| `execve_prompt_rejection_keeps_unmatched_commands_on_sandbox_flag` | Granular 策略中 `sandbox_approval=false` 时拒绝未匹配命令的审批 |
| `approval_sandbox_permissions_only_downgrades_preapproved_additional_permissions` | 预审批的 additional_permissions 降级为 UseDefault |
| `extract_shell_script_preserves_login_flag` | 正确解析 `-lc` 和 `-c` 标志 |
| `extract_shell_script_supports_wrapped_command_prefixes` | 支持包含 env/sandbox-exec 前缀的命令解析 |
| `extract_shell_script_rejects_unsupported_shell_invocation` | 拒绝不支持的 shell 调用格式 |
| `join_program_and_argv_replaces_original_argv_zero` | 用绝对路径替换 argv[0] |
| `commands_for_intercepted_exec_policy_parses_plain_shell_wrappers` | 解析简单 shell 包装器中的多个命令 |
| `map_exec_result_preserves_stdout_and_stderr` | 执行结果正确保留 stdout、stderr 和聚合输出 |
| `shell_request_escalation_execution_is_explicit` | 不同 SandboxPermissions 对应正确的 EscalationExecution |
| `evaluate_intercepted_exec_policy_*` | 多个 exec policy 评估场景的验证 |
