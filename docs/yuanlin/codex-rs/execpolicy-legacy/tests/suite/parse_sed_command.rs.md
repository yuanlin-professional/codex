# `parse_sed_command.rs` — 测试模块

## 测试覆盖范围

测试 `parse_sed_command` 函数对 sed 命令字符串的安全性解析。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `parses_simple_print_command` | `122,202p` 格式的行范围打印命令应被接受 |
| `rejects_malformed_print_command` | `122,202`（缺少 p 后缀）和 `122202`（缺少逗号和 p）均应被拒绝，返回 SedCommandNotProvablySafe 错误 |
