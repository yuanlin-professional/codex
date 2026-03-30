# `ls.rs` — 测试模块

## 测试覆盖范围

针对 `ls` 命令的策略检查测试，覆盖无参数、标志组合、未知选项、捆绑选项、文件参数以及标志与文件参数混合等场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_ls_no_args` | ls 无参数：匹配成功，返回空参数列表 |
| `test_ls_dash_a_dash_l` | ls -a -l：两个标志均匹配成功 |
| `test_ls_dash_z` | ls -z：未定义的选项，返回 UnknownOption 错误 |
| `test_ls_dash_al` | ls -al：捆绑选项暂不支持，返回 UnknownOption 错误 |
| `test_ls_one_file_arg` | ls foo：单文件参数匹配为 ReadableFile |
| `test_ls_multiple_file_args` | ls foo bar baz：多文件参数匹配为多个 ReadableFile |
| `test_ls_multiple_flags_and_file_args` | ls -l -a foo bar baz：标志和文件参数混合匹配 |
| `test_flags_after_file_args` | ls foo -l：文件参数后的标志仍被正确识别（注：ls 实际不支持此用法） |
