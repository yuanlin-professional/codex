# `js_repl_tests.rs` -- 测试模块

## 测试覆盖范围
覆盖 `js_repl.rs` 中 freeform 参数解析逻辑和执行事件发送功能的正确性。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `parse_freeform_args_without_pragma` | 验证不含 pragma 前缀的纯 JavaScript 代码被正确解析，`timeout_ms` 为 None |
| `parse_freeform_args_with_pragma` | 验证带 `// codex-js-repl: timeout_ms=15000` 前缀的输入能正确提取超时参数和代码部分 |
| `parse_freeform_args_rejects_unknown_key` | 验证 pragma 中包含未知键（如 `nope`）时返回错误 |
| `parse_freeform_args_rejects_reset_key` | 验证 pragma 中使用已废弃的 `reset` 键时返回错误（reset 已独立为 `js_repl_reset` 工具） |
| `parse_freeform_args_rejects_json_wrapped_code` | 验证 JSON 格式的输入（如 `{"code":"..."}`)被拒绝，引导模型发送原始 JavaScript 代码 |
| `emit_js_repl_exec_end_sends_event` | 异步测试：验证 `emit_js_repl_exec_end` 发送的 `ExecCommandEnd` 事件包含正确的 call_id、命令、工作目录、输出内容和耗时 |
