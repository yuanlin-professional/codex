# `exec_policy_tests.rs` — 测试模块

## 测试覆盖范围

命令执行策略管理器（ExecPolicyManager）的策略加载、规则评估、审批需求判定、Starlark 策略文件解析、主机可执行文件路径匹配、策略修正提案生成，以及沙箱权限交互逻辑。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `child_uses_parent_exec_policy_when_layer_stack_matches` | 子配置与父配置层栈匹配时使用父执行策略 |
| `child_uses_parent_exec_policy_when_non_exec_policy_layers_differ` | 非执行策略层不同时仍使用父执行策略 |
| `child_does_not_use_parent_exec_policy_when_requirements_exec_policy_differs` | 需求执行策略不同时子配置不使用父策略 |
| `returns_empty_policy_when_no_policy_files_exist` | 无策略文件时返回空策略 |
| `collect_policy_files_returns_empty_when_dir_missing` | 策略目录缺失时返回空列表 |
| `format_exec_policy_error_with_source_renders_range` | 格式化执行策略错误时渲染行范围信息 |
| `parse_starlark_line_from_message_extracts_path_and_line` | 从 Starlark 错误消息中提取文件路径和行号 |
| `parse_starlark_line_from_message_rejects_zero_line` | 拒绝行号为零的 Starlark 错误消息 |
| `loads_policies_from_policy_subdirectory` | 从策略子目录加载策略规则 |
| `merges_requirements_exec_policy_network_rules` | 合并需求执行策略的网络规则 |
| `preserves_host_executables_when_requirements_overlay_is_present` | 存在需求覆盖层时保留主机可执行文件 |
| `ignores_policies_outside_policy_dir` | 忽略策略目录外的策略文件 |
| `ignores_rules_from_untrusted_project_layers` | 忽略不受信任的项目层规则 |
| `loads_policies_from_multiple_config_layers` | 从多个配置层加载策略 |
| `evaluates_bash_lc_inner_commands` | 评估 bash -lc 内部命令 |
| `commands_for_exec_policy_falls_back_for_empty_shell_script` | 空 shell 脚本时回退到原始命令 |
| `commands_for_exec_policy_falls_back_for_whitespace_shell_script` | 纯空白 shell 脚本时回退到原始命令 |
| `evaluates_heredoc_script_against_prefix_rules` | 对 heredoc 脚本评估前缀规则 |
| `omits_auto_amendment_for_heredoc_fallback_prompts` | heredoc 回退提示时省略自动修正 |
| `drops_requested_amendment_for_heredoc_fallback_prompts_when_it_wont_match` | heredoc 回退提示不匹配时丢弃请求的修正 |
| `justification_is_included_in_forbidden_exec_approval_requirement` | 禁止执行的审批需求中包含理由说明 |
| `exec_approval_requirement_prefers_execpolicy_match` | 执行审批需求优先匹配执行策略 |
| `absolute_path_exec_approval_requirement_matches_host_executable_rules` | 绝对路径匹配主机可执行文件规则 |
| `absolute_path_exec_approval_requirement_ignores_disallowed_host_executable_paths` | 绝对路径忽略不允许的主机可执行文件路径 |
| `requested_prefix_rule_can_approve_absolute_path_commands` | 请求的前缀规则可审批绝对路径命令 |
| `exec_approval_requirement_respects_approval_policy` | 执行审批需求尊重审批策略设置 |
| `unmatched_granular_policy_still_prompts_for_restricted_sandbox_escalation` | 未匹配的细粒度策略仍为受限沙箱升级提示 |
| `unmatched_on_request_uses_split_filesystem_policy_for_escalation_prompts` | 未匹配的 OnRequest 策略使用分离文件系统策略进行升级提示 |
| `exec_approval_requirement_prompts_for_inline_additional_permissions_under_on_request` | OnRequest 下内联附加权限触发审批提示 |
| `exec_approval_requirement_rejects_unmatched_sandbox_escalation_when_granular_sandbox_is_disabled` | 细粒度沙箱禁用时拒绝未匹配的沙箱升级 |
| `mixed_rule_and_sandbox_prompt_prioritizes_rule_for_rejection_decision` | 混合规则和沙箱提示时优先使用规则拒绝决策 |
| `mixed_rule_and_sandbox_prompt_rejects_when_granular_rules_are_disabled` | 细粒度规则禁用时混合提示被拒绝 |
| `exec_approval_requirement_falls_back_to_heuristics` | 执行审批需求回退到启发式规则 |
| `empty_bash_lc_script_falls_back_to_original_command` | 空 bash -lc 脚本回退到原始命令 |
| `whitespace_bash_lc_script_falls_back_to_original_command` | 纯空白 bash -lc 脚本回退到原始命令 |
| `request_rule_uses_prefix_rule` | 请求规则使用前缀规则 |
| `request_rule_falls_back_when_prefix_rule_does_not_approve_all_commands` | 前缀规则未批准所有命令时回退 |
| `heuristics_apply_when_other_commands_match_policy` | 其他命令匹配策略时启发式规则仍适用 |
| `append_execpolicy_amendment_updates_policy_and_file` | 追加执行策略修正更新策略和文件 |
| `append_execpolicy_amendment_rejects_empty_prefix` | 追加执行策略修正拒绝空前缀 |
| `proposed_execpolicy_amendment_is_present_for_single_command_without_policy_match` | 单命令无策略匹配时存在策略修正提案 |
| `proposed_execpolicy_amendment_is_omitted_when_policy_prompts` | 策略提示时省略策略修正提案 |
| `proposed_execpolicy_amendment_is_present_for_multi_command_scripts` | 多命令脚本存在策略修正提案 |
| `proposed_execpolicy_amendment_uses_first_no_match_in_multi_command_scripts` | 多命令脚本使用第一个未匹配命令的修正提案 |
| `proposed_execpolicy_amendment_is_present_when_heuristics_allow` | 启发式允许时存在策略修正提案 |
| `proposed_execpolicy_amendment_is_suppressed_when_policy_matches_allow` | 策略匹配允许时抑制策略修正提案 |
| `derive_requested_execpolicy_amendment_returns_none_for_missing_prefix_rule` | 缺少前缀规则时返回 None |
| `derive_requested_execpolicy_amendment_returns_none_for_empty_prefix_rule` | 空前缀规则时返回 None |
| `derive_requested_execpolicy_amendment_returns_none_for_exact_banned_prefix_rule` | 精确禁止的前缀规则返回 None |
| `derive_requested_execpolicy_amendment_returns_none_for_windows_and_pypy_variants` | Windows 和 PyPy 变体返回 None |
| `derive_requested_execpolicy_amendment_returns_none_for_shell_and_powershell_variants` | Shell 和 PowerShell 变体返回 None |
| `derive_requested_execpolicy_amendment_allows_non_exact_banned_prefix_rule_match` | 非精确禁止的前缀规则匹配允许修正 |
| `derive_requested_execpolicy_amendment_returns_none_when_policy_matches` | 策略匹配时返回 None |
| `dangerous_rm_rf_requires_approval_in_danger_full_access` | 危险的 rm -rf 在完全访问模式下需要审批 |
| `verify_approval_requirement_for_unsafe_powershell_command` | 验证不安全 PowerShell 命令的审批需求 |
| `dangerous_command_allowed_when_sandbox_is_explicitly_disabled` | 明确禁用沙箱时允许危险命令 |
| `dangerous_command_forbidden_in_external_sandbox_when_policy_matches` | 外部沙箱中策略匹配时禁止危险命令 |
