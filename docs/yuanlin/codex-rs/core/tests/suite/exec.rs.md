# `exec.rs` — 测试模块

## 测试覆盖范围

测试命令执行(exec)功能在 macOS seatbelt 沙箱环境下的行为，包括正常命令执行、输出截断（按行和按字节）、命令未找到的处理，以及沙箱策略对文件写入的阻止。仅在 macOS 平台运行。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `exit_code_0_succeeds` | 命令正常执行返回退出码 0，验证 stdout 和 stderr 内容 |
| `truncates_output_lines` | 执行产生 300 行输出的命令，验证输出完整性 |
| `truncates_output_bytes` | 执行产生大量字节输出(每行 1000 字节)的命令，验证字节级输出 |
| `exit_command_not_found_is_ok` | 命令未找到(退出码 127)不被视为沙箱错误 |
| `write_file_fails_as_sandbox_error` | 在只读沙箱策略下写文件失败，视为沙箱错误 |
