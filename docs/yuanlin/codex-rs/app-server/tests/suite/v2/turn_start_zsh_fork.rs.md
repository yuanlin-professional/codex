# `turn_start_zsh_fork.rs` — 测试模块

## 测试覆盖范围
测试 zsh fork shell 模式下的 `turn/start` 行为，验证命令执行、执行审批拒绝、
审批取消，以及子命令拒绝标记父命令为已拒绝。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `turn_start_shell_zsh_fork_executes_command_v2` | zsh fork 模式执行命令 |
| `turn_start_shell_zsh_fork_exec_approval_decline_v2` | zsh fork 模式执行审批拒绝 |
| `turn_start_shell_zsh_fork_exec_approval_cancel_v2` | zsh fork 模式执行审批取消 |
| `turn_start_shell_zsh_fork_subcommand_decline_marks_parent_declined_v2` | 子命令拒绝标记父命令为已拒绝 |
