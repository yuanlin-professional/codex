# `mod_tests.rs` — 测试模块

## 测试覆盖范围
覆盖 JavaScript REPL 模块的核心功能，包括 Node 版本解析、REPL 执行、嵌套工具调用、图像发射、动态工具支持、执行中断、stderr 缓冲区管理和内核故障诊断等。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `node_version_parses_v_prefix_and_suffix` | 解析带 `v` 前缀和后缀（如 nightly）的 Node 版本号 |
| `js_repl_echo` | 基本的 JavaScript 代码执行和输出验证 |
| `js_repl_error` | JavaScript 执行错误时的错误信息返回 |
| `js_repl_timeout` | 执行超时后内核重置并返回超时错误 |
| `js_repl_persistence_across_invocations` | 多次调用间变量状态持久化 |
| `js_repl_tool_call` | 嵌套工具调用（如 shell 命令）的完整流程 |
| `js_repl_emit_image` | 通过 `codex.emitImage` 发射 data URL 图像 |
| `js_repl_dynamic_tool_call` | 调用动态注册的工具 |
| `js_repl_freeform_tool_call` | 调用 freeform 类型的自定义工具 |
| `js_repl_interrupt_resets_kernel` | 执行中断后正确重置内核 |
| `push_stderr_tail_line_*` | stderr 缓冲区的行限制和字节限制逻辑 |
| `format_model_kernel_failure_details_*` | 内核故障诊断信息的格式化 |
| `validate_emitted_image_url_*` | 图像 URL 验证（仅接受 data: 协议） |
| `is_kernel_status_exited_*` | 内核退出状态检测 |
