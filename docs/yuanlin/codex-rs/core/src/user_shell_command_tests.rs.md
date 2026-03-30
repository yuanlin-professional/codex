# `user_shell_command_tests.rs` — 测试模块

## 测试覆盖范围

测试用户 shell 命令记录的格式化逻辑，包括 `USER_SHELL_COMMAND_FRAGMENT` 的文本匹配、`user_shell_command_record_item` 的 XML 格式输出，以及 `format_user_shell_command_record` 在有聚合输出时的优先使用行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `detects_user_shell_command_text_variants` | `<user_shell_command>` 标签包裹的文本可被正确识别，普通文本不会被误判 |
| `formats_basic_record` | 基本命令执行结果被格式化为包含 `<command>`、`<result>`（退出码、耗时、输出）的 XML 结构 |
| `uses_aggregated_output_over_streams` | 当存在聚合输出（`aggregated_output`）时，优先使用聚合输出而非独立的 stdout/stderr |
