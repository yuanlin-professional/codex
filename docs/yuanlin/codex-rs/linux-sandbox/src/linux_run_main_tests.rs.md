# `linux_run_main_tests.rs` -- 测试模块

## 测试覆盖范围

覆盖 `linux_run_main` 模块中 bubblewrap 参数构建、内部命令 argv0 处理、网络模式选择、沙箱策略解析/验证、以及 seccomp 与 landlock 模式兼容性检查。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `detects_proc_mount_invalid_argument_failure` | 检测 `/proc` 挂载失败（Invalid argument） |
| `detects_proc_mount_operation_not_permitted_failure` | 检测 `/proc` 挂载失败（Operation not permitted） |
| `detects_proc_mount_permission_denied_failure` | 检测 `/proc` 挂载失败（Permission denied） |
| `ignores_non_proc_mount_errors` | 非 `/proc` 挂载错误不会被误判 |
| `inserts_bwrap_argv0_before_command_separator` | 支持 `--argv0` 时在命令分隔符前正确插入 |
| `rewrites_inner_command_path_when_bwrap_lacks_argv0` | 不支持 `--argv0` 时直接替换内部命令路径 |
| `rewrites_bwrap_helper_command_not_nested_user_command_when_current_exe_appears_later` | 仅替换辅助程序路径而非嵌套的用户命令 |
| `inserts_unshare_net_when_network_isolation_requested` | 网络隔离模式下插入 `--unshare-net` |
| `inserts_unshare_net_when_proxy_only_network_mode_requested` | 代理模式下也插入 `--unshare-net` |
| `proxy_only_mode_takes_precedence_over_full_network_policy` | 代理模式优先于完整网络策略 |
| `split_only_filesystem_policy_requires_direct_runtime_enforcement` | 仅 split 文件系统策略需要直接运行时执行 |
| `root_write_read_only_carveout_requires_direct_runtime_enforcement` | 根写入+只读例外需要直接运行时执行 |
| `managed_proxy_preflight_argv_is_wrapped_for_full_access_policy` | 托管代理预检命令包含正确的 bwrap 包装 |
| `managed_proxy_inner_command_includes_route_spec` | 托管代理的内部命令包含路由规范 |
| `inner_command_includes_split_policy_flags` | 内部命令包含 split-policy 标志 |
| `non_managed_inner_command_omits_route_spec` | 非托管模式省略路由规范 |
| `managed_proxy_inner_command_requires_route_spec` | 托管代理模式缺少路由规范时 panic |
| `resolve_sandbox_policies_derives_split_policies_from_legacy_policy` | 从旧策略正确派生 split 策略 |
| `resolve_sandbox_policies_derives_legacy_policy_from_split_policies` | 从 split 策略正确反向派生旧策略 |
| `resolve_sandbox_policies_rejects_partial_split_policies` | 拒绝不完整的 split 策略 |
| `resolve_sandbox_policies_rejects_mismatched_legacy_and_split_inputs` | 拒绝不匹配的旧策略与 split 策略 |
| `resolve_sandbox_policies_accepts_split_policies_requiring_direct_runtime_enforcement` | 接受需要直接运行时执行的 split 策略 |
| `resolve_sandbox_policies_accepts_semantically_equivalent_workspace_write_inputs` | 接受语义等价的 workspace-write 策略 |
| `apply_seccomp_then_exec_with_legacy_landlock_panics` | seccomp 模式与旧 landlock 不兼容时 panic |
| `legacy_landlock_rejects_split_only_filesystem_policies` | 旧 landlock 拒绝需要直接执行的 split 策略 |
| `valid_inner_stage_modes_do_not_panic` | 有效的内部阶段模式组合不会 panic |
