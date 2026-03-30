# `head.rs` — 测试模块

## 测试覆盖范围

针对 `head` 命令的策略检查测试，覆盖无参数、单文件、带 `-n` 选项以及各种无效 `-n` 值的场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_head_no_args` | head 无参数：应返回 VarargMatcherDidNotMatchAnything 错误 |
| `test_head_one_file_no_flags` | head 单文件：匹配成功，文件类型为 ReadableFile |
| `test_head_one_flag_one_file` | head -n 100 file：选项值为 PositiveInteger，文件为 ReadableFile |
| `test_head_invalid_n_as_0` | head -n 0：0 不是有效正整数，返回 InvalidPositiveInteger 错误 |
| `test_head_invalid_n_as_nonint_float` | head -n 1.5：浮点数不是有效正整数 |
| `test_head_invalid_n_as_float` | head -n 1.0：整数浮点数也不是有效正整数 |
| `test_head_invalid_n_as_negative_int` | head -n -1：负数被识别为选项而非值，返回 OptionFollowedByOptionInsteadOfValue 错误 |
