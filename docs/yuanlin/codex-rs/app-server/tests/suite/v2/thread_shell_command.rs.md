# `thread_shell_command.rs` — 测试模块

## 测试覆盖范围
测试 `thread/shellCommand` JSON-RPC 方法，验证独立 turn 执行 shell 命令并持久化历史，
以及在已有活跃 turn 时使用该 turn 执行命令。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_shell_command_runs_as_standalone_turn_and_persists_history` | 独立 turn 执行 shell 命令并持久化 |
| `thread_shell_command_uses_existing_active_turn` | 使用已有活跃 turn 执行命令 |
