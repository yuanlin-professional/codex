# `exec_tests.rs` — 测试模块

## 测试覆盖范围

命令执行模块（exec），包括沙箱检测、输出读取与截断、聚合输出策略、完整缓冲区捕获模式、Windows 受限令牌沙箱策略、进程超时终止、取消令牌支持。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `sandbox_detection_requires_keywords` | 沙箱检测需要关键字才能判定为沙箱拒绝 |
| `sandbox_detection_identifies_keyword_in_stderr` | 在 stderr 中识别沙箱拒绝关键字 |
| `sandbox_detection_respects_quick_reject_exit_codes` | 沙箱检测尊重快速拒绝退出码 |
| `sandbox_detection_ignores_non_sandbox_mode` | 非沙箱模式下忽略沙箱检测 |
| `sandbox_detection_ignores_network_policy_text_in_non_sandbox_mode` | 非沙箱模式下忽略网络策略文本 |
| `sandbox_detection_uses_aggregated_output` | 沙箱检测使用聚合输出 |
| `sandbox_detection_ignores_network_policy_text_with_zero_exit_code` | 退出码为零时忽略网络策略文本 |
| `read_output_limits_retained_bytes_for_shell_capture` | 读取输出时限制 shell 捕获的保留字节数 |
| `aggregate_output_prefers_stderr_on_contention` | 聚合输出在争用时优先使用 stderr |
| `aggregate_output_fills_remaining_capacity_with_stderr` | 聚合输出用 stderr 填充剩余容量 |
| `aggregate_output_rebalances_when_stderr_is_small` | stderr 较小时聚合输出重新平衡 |
| `aggregate_output_keeps_stdout_then_stderr_when_under_cap` | 未超限时聚合输出保持 stdout 在前 stderr 在后 |
| `read_output_retains_all_bytes_for_full_buffer_capture` | 完整缓冲区捕获模式保留所有字节 |
| `aggregate_output_keeps_all_bytes_when_uncapped` | 无上限时聚合输出保留所有字节 |
| `full_buffer_capture_policy_disables_caps_and_exec_expiration` | 完整缓冲区捕获策略禁用上限和执行过期 |
| `exec_full_buffer_capture_ignores_expiration` | 完整缓冲区捕获忽略超时过期 |
| `exec_full_buffer_capture_keeps_io_drain_timeout_when_descendant_holds_pipe_open` | 后代进程持有管道时保持 IO 排空超时 |
| `process_exec_tool_call_preserves_full_buffer_capture_policy` | 处理执行工具调用时保留完整缓冲区捕获策略 |
| `windows_restricted_token_skips_external_sandbox_policies` | Windows 受限令牌跳过外部沙箱策略 |
| `windows_restricted_token_runs_for_legacy_restricted_policies` | Windows 受限令牌运行传统受限策略 |
| `windows_restricted_token_rejects_network_only_restrictions` | Windows 受限令牌拒绝仅网络限制 |
| `windows_restricted_token_allows_legacy_restricted_policies` | Windows 受限令牌允许传统受限策略 |
| `windows_restricted_token_allows_legacy_workspace_write_policies` | Windows 受限令牌允许传统工作区写入策略 |
| `windows_restricted_token_rejects_split_only_filesystem_policies` | Windows 受限令牌拒绝仅分离文件系统策略 |
| `windows_restricted_token_rejects_root_write_read_only_carveouts` | Windows 受限令牌拒绝根写入只读例外 |
| `windows_restricted_token_supports_full_read_split_write_read_carveouts` | Windows 受限令牌支持完整读取分离写入读取例外 |
| `windows_elevated_rejects_split_write_read_carveouts` | Windows 提升权限拒绝分离写入读取例外 |
| `process_exec_tool_call_uses_platform_sandbox_for_network_only_restrictions` | 仅网络限制时使用平台沙箱 |
| `sandbox_detection_flags_sigsys_exit_code` | 沙箱检测标记 SIGSYS 退出码（Unix） |
| `kill_child_process_group_kills_grandchildren_on_timeout` | 超时时终止子进程组包括孙进程（Unix） |
| `process_exec_tool_call_respects_cancellation_token` | 处理执行工具调用尊重取消令牌 |
