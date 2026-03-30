# `sed.rs` — 测试模块

## 测试覆盖范围

针对 `sed` 命令的策略检查测试，覆盖安全打印命令、带 `-e` 标志、危险命令拒绝以及必需选项缺失场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_sed_print_specific_lines` | sed -n 122,202p hello.txt：安全的行范围打印命令，匹配成功 |
| `test_sed_print_specific_lines_with_e_flag` | sed -n -e 122,202p hello.txt：带 `-e` 选项的安全命令，选项值为 SedCommand 类型 |
| `test_sed_reject_dangerous_command` | sed -e s/y/echo hi/e hello.txt：含 `/e` 执行替换的危险命令，返回 SedCommandNotProvablySafe 错误 |
| `test_sed_verify_e_or_pattern_is_required` | sed 122,202p（缺少 `-e` 选项）：应返回 MissingRequiredOptions 错误 |
