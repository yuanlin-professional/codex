# `cp.rs` — 测试模块

## 测试覆盖范围

针对 `cp` 命令的策略检查测试，覆盖无参数、单参数、双文件和多文件拷贝场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_cp_no_args` | cp 无参数：应返回 NotEnoughArgs 错误 |
| `test_cp_one_arg` | cp 单参数：变参匹配器无法匹配，应返回 VarargMatcherDidNotMatchAnything 错误 |
| `test_cp_one_file` | cp 两个文件参数：第一个为可读文件，第二个为可写文件，匹配成功 |
| `test_cp_multiple_files` | cp 三个文件参数：前两个为可读文件，最后一个为可写文件，匹配成功 |
