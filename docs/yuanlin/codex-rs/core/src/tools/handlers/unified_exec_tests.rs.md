# `unified_exec_tests.rs` — 测试模块

## 测试覆盖范围
测试覆盖了 `UnifiedExecHandler` 的命令构建（`get_command`）、shell 模式选择、登录 shell 配置、相对路径权限解析，以及 pre/post tool use payload 的生成逻辑。验证了 Direct 和 ZshFork 两种 shell 模式以及各种边界条件。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `test_get_command_uses_default_shell_when_unspecified` | 验证未指定 shell 时使用默认用户 shell，生成三段式命令 |
| `test_get_command_respects_explicit_bash_shell` | 验证显式指定 `/bin/bash` 时正确生成 bash 命令 |
| `test_get_command_respects_explicit_powershell_shell` | 验证指定 `powershell` 时正确生成 PowerShell 命令 |
| `test_get_command_respects_explicit_cmd_shell` | 验证指定 `cmd` 时正确生成 cmd 命令 |
| `test_get_command_rejects_explicit_login_when_disallowed` | 验证禁用登录 shell 时显式 `login: true` 被拒绝并返回正确错误消息 |
| `test_get_command_ignores_explicit_shell_in_zsh_fork_mode` | 验证 ZshFork 模式下忽略模型指定的 shell，使用配置的 zsh 路径 |
| `exec_command_args_resolve_relative_additional_permissions_against_workdir` | 验证 additional_permissions 中的相对路径基于 workdir 正确解析为绝对路径 |
| `exec_command_pre_tool_use_payload_uses_raw_command` | 验证 `exec_command` 的 pre payload 使用原始 cmd 字符串 |
| `exec_command_pre_tool_use_payload_skips_write_stdin` | 验证 `write_stdin` 工具名不生成 pre payload |
| `exec_command_post_tool_use_payload_uses_output_for_noninteractive_one_shot_commands` | 验证非 TTY 的一次性命令生成包含原始输出的 post payload |
| `exec_command_post_tool_use_payload_skips_interactive_exec` | 验证 TTY 模式命令不生成 post payload |
| `exec_command_post_tool_use_payload_skips_running_sessions` | 验证仍在运行的进程（有 process_id 无 exit_code）不生成 post payload |
