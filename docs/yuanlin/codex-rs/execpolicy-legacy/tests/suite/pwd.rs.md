# `pwd.rs` — 测试模块

## 测试覆盖范围

针对 `pwd` 命令的策略检查测试，覆盖无参数、`-L`、`-P` 标志以及多余参数的场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_pwd_no_args` | pwd 无参数：匹配成功 |
| `test_pwd_capital_l` | pwd -L：标志匹配成功 |
| `test_pwd_capital_p` | pwd -P：标志匹配成功 |
| `test_pwd_extra_args` | pwd foo bar：多余的位置参数应返回 UnexpectedArguments 错误 |
