# `shell_tests.rs` — 测试模块

## 测试覆盖范围
测试覆盖了 `ShellHandler` 和 `ShellCommandHandler` 的命令生成、安全命令识别、登录 shell 配置、ExecParams 构建，以及 pre/post tool use payload 的构建逻辑。涉及多种 shell 类型（Bash、Zsh、PowerShell）。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `commands_generated_by_shell_command_handler_can_be_matched_by_is_known_safe_command` | 验证各 shell 类型（Bash、Zsh、PowerShell）生成的安全命令能被 `is_known_safe_command` 正确识别 |
| `shell_command_handler_to_exec_params_uses_session_shell_and_turn_context` | 验证 `to_exec_params` 正确使用 session 的 shell、turn context 的工作目录和环境策略构建参数 |
| `shell_command_handler_respects_explicit_login_flag` | 验证 `base_command` 在登录/非登录模式下生成正确的 shell 参数 |
| `shell_command_handler_defaults_to_non_login_when_disallowed` | 验证禁用登录 shell 时，默认使用非登录模式生成命令 |
| `shell_command_handler_rejects_login_when_disallowed` | 验证禁用登录 shell 时，显式请求登录被拒绝 |
| `shell_pre_tool_use_payload_uses_joined_command` | 验证 `ShellHandler` 对 `LocalShell` payload 使用 shlex_join 拼接命令 |
| `shell_command_pre_tool_use_payload_uses_raw_command` | 验证 `ShellCommandHandler` 直接使用原始命令字符串 |
| `build_post_tool_use_payload_uses_tool_output_wire_value` | 验证 post_tool_use_payload 从工具输出中提取正确的 wire 值 |
