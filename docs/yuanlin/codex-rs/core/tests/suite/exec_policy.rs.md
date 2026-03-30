# `exec_policy.rs` — 测试模块

## 测试覆盖范围

测试执行策略(Exec Policy)功能，包括自定义策略规则阻止特定命令、协作模式(Collaboration Mode)下空脚本和空白脚本的安全处理（不触发 panic），覆盖 shell_command 和 unified exec_command 两种工具类型。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `execpolicy_blocks_shell_invocation` | 自定义 prefix_rule 策略阻止以 "echo" 开头的 shell 命令 |
| `shell_command_empty_script_with_collaboration_mode_does_not_panic` | 协作模式下执行空字符串 shell_command 不触发 panic |
| `unified_exec_empty_script_with_collaboration_mode_does_not_panic` | 协作模式+UnifiedExec 下执行空字符串 exec_command 不触发 panic |
| `shell_command_whitespace_script_with_collaboration_mode_does_not_panic` | 协作模式下执行纯空白字符 shell_command 不触发 panic |
| `unified_exec_whitespace_script_with_collaboration_mode_does_not_panic` | 协作模式+UnifiedExec 下执行纯空白字符 exec_command 不触发 panic |
