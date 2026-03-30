# `environment_context_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖环境上下文（EnvironmentContext）的 XML 序列化和相等性比较功能，包括不同沙箱模式下的序列化输出、网络上下文（allowed/denied domains）的序列化、子代理（subagents）信息的序列化，以及 `equals_except_shell` 方法的语义正确性。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `serialize_workspace_write_environment_context` | 验证工作区写入模式下环境上下文的 XML 序列化输出包含 cwd、shell、日期和时区 |
| `serialize_environment_context_with_network` | 验证包含网络上下文（允许和拒绝域名列表）时的 XML 序列化输出 |
| `serialize_read_only_environment_context` | 验证只读模式（无 cwd）下环境上下文的 XML 序列化输出 |
| `serialize_external_sandbox_environment_context` | 验证外部沙箱模式下环境上下文的 XML 序列化输出 |
| `serialize_external_sandbox_with_restricted_network_environment_context` | 验证外部沙箱受限网络模式下环境上下文的 XML 序列化输出 |
| `serialize_full_access_environment_context` | 验证完全访问模式下环境上下文的 XML 序列化输出 |
| `equals_except_shell_compares_cwd` | 验证 equals_except_shell 方法正确比较相同 cwd |
| `equals_except_shell_ignores_sandbox_policy` | 验证 equals_except_shell 方法忽略沙箱策略差异 |
| `equals_except_shell_compares_cwd_differences` | 验证 equals_except_shell 方法正确检测不同 cwd |
| `equals_except_shell_ignores_shell` | 验证 equals_except_shell 方法忽略 shell 类型差异（Bash vs Zsh） |
| `serialize_environment_context_with_subagents` | 验证包含子代理信息时的 XML 序列化输出 |
