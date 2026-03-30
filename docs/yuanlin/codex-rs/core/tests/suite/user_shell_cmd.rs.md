# `user_shell_cmd.rs` -- 测试模块

## 测试覆盖范围
测试用户 shell 命令（bang command）的执行功能，包括基本的 ls/cat 执行、中断处理、与活跃 turn 的并发行为、命令历史持久化及与模型共享、网络沙箱环境变量隔离、输出截断和双重截断防护等。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `user_shell_cmd_ls_and_cat_in_temp_dir` | 在临时目录中执行 ls 和 cat 命令，验证输出正确 |
| `user_shell_cmd_can_be_interrupted` | 执行长命令后发送 Interrupt，验证收到 TurnAborted(Interrupted) |
| `user_shell_command_does_not_replace_active_turn` | 活跃 turn 执行中提交用户 shell 命令，不替换当前 turn，两者并行完成 |
| `user_shell_command_history_is_persisted_and_shared_with_model` | 用户 shell 命令的输出以 `<user_shell_command>` XML 格式记录在后续模型请求中 |
| `user_shell_command_does_not_set_network_sandbox_env_var` | 用户 shell 命令不设置 CODEX_SANDBOX_NETWORK_DISABLED 环境变量 |
| `user_shell_command_output_is_truncated_in_history` | 输出超过 token 限制时在历史中被截断（仅非 Windows） |
| `user_shell_command_is_truncated_only_once` | 确保截断只发生一次，不出现双重截断标记 |
