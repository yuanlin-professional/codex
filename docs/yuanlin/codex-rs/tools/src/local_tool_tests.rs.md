# `local_tool_tests.rs` -- 测试模块

## 测试覆盖范围
验证本地命令执行工具（exec_command、shell、write_stdin、request_permissions）的 ToolSpec 定义、JSON 序列化和各种配置组合。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `exec_command_tool_serializes` | exec_command 工具 JSON 序列化 |
| `exec_command_tool_with_login_shell` | 启用 login_shell 时包含 login 参数 |
| `exec_command_tool_exec_policy_hint` | 包含 exec_policy_hint 参数 |
| `shell_tool_serializes` | shell 工具 JSON 序列化 |
| `write_stdin_tool_serializes` | write_stdin 工具序列化 |
| `request_permissions_tool_serializes` | request_permissions 工具序列化 |
