# `execute_handler_tests.rs` — 测试模块

## 测试覆盖范围
测试 `parse_freeform_args` 函数对 Code Mode 执行参数的解析逻辑，包括 pragma 指令处理。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `parse_freeform_args_without_pragma` | 不含 pragma 指令时，代码原样返回，`yield_time_ms` 和 `max_output_tokens` 为 None |
| `parse_freeform_args_with_pragma` | 包含 `@exec` pragma 指令时，正确解析 `yield_time_ms` 和 `max_output_tokens` |
| `parse_freeform_args_rejects_unknown_key` | 拒绝 pragma 中的未知键（如 `nope`），返回明确错误信息 |
| `parse_freeform_args_rejects_missing_source` | pragma 后无 JavaScript 源码时返回错误 |
