# `code_mode.rs` — 测试模块

## 测试覆盖范围

测试 Code Mode（代码执行模式）功能，包括通过 exec 工具执行 JavaScript 代码、嵌套工具调用(exec_command/update_plan/apply_patch/view_image)、并行工具执行、输出截断、脚本失败处理、yield/resume/terminate 流程、多会话管理、全局辅助函数(text/image/notify/exit/store/load)、MCP 工具集成、隐藏动态工具，以及 code_mode_only 模式限制。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `code_mode_can_return_exec_command_output` | 通过 exec 执行 exec_command 并返回输出结果 |
| `code_mode_only_restricts_prompt_tools` | code_mode_only 模式仅暴露 exec 和 wait 工具 |
| `code_mode_only_can_call_nested_tools` | code_mode_only 模式下可调用嵌套工具 |
| `code_mode_update_plan_nested_tool_result_is_empty_object` | 嵌套调用 update_plan 返回空对象 |
| `code_mode_nested_tool_calls_can_run_in_parallel` | 嵌套工具调用可并行执行(使用 Promise.all) |
| `code_mode_can_truncate_final_result_with_configured_budget` | 根据配置的 max_output_tokens 截断最终结果 |
| `code_mode_returns_accumulated_output_when_script_fails` | 脚本失败时返回已累积的输出和错误信息 |
| `code_mode_exec_surfaces_handler_errors_as_exceptions` | 嵌套工具处理器错误作为 JavaScript 异常被捕获 |
| `code_mode_can_yield_and_resume_with_wait` | 通过 yield_control 暂停执行，使用 wait 工具恢复 |
| `code_mode_yield_timeout_works_for_busy_loop` | yield 超时机制对无限循环有效 |
| `code_mode_can_run_multiple_yielded_sessions` | 可同时运行多个暂停的会话 |
| `code_mode_wait_can_terminate_and_continue` | wait 工具可终止暂停的会话后继续新的执行 |
| `code_mode_wait_returns_error_for_unknown_session` | 对未知 cell_id 调用 wait 返回错误 |
| `code_mode_wait_terminate_returns_completed_session_if_it_finished_after_yield_control` | 终止已自然完成的会话时返回完成结果 |
| `code_mode_background_keeps_running_on_later_turn_without_wait` | yield 后的脚本在后续轮次中无需 wait 即可继续运行 |
| `code_mode_wait_uses_its_own_max_tokens_budget` | wait 工具使用自身的 max_tokens 预算截断输出 |
| `code_mode_can_output_serialized_text_via_global_helper` | 全局 text() 辅助函数可输出序列化文本 |
| `code_mode_notify_injects_additional_exec_tool_output_into_active_context` | notify() 辅助函数将额外输出注入活跃上下文 |
| `code_mode_exit_stops_script_immediately` | exit() 辅助函数立即停止脚本执行 |
| `code_mode_surfaces_text_stringify_errors` | 循环引用对象的 text() 调用正确报告序列化错误 |
| `code_mode_can_output_images_via_global_helper` | image() 辅助函数可输出 URL 和 data URI 格式的图片 |
| `code_mode_can_use_view_image_result_with_image_helper` | 将 view_image 工具结果通过 image() 辅助函数输出 |
| `code_mode_can_apply_patch_via_nested_tool` | 通过嵌套工具调用 apply_patch |
| `code_mode_can_print_structured_mcp_tool_result_fields` | 打印 MCP 工具(rmcp echo)的结构化结果字段 |
| `code_mode_exposes_mcp_tools_on_global_tools_object` | MCP 工具暴露在全局 tools 对象上 |
| `code_mode_exposes_namespaced_mcp_tools_on_global_tools_object` | 命名空间化的 MCP 工具暴露在全局 tools 对象上 |
| `code_mode_exposes_normalized_illegal_mcp_tool_names` | 规范化非法字符的 MCP 工具名可正常调用 |
| `code_mode_lists_global_scope_items` | 列出全局作用域中的所有项目 |
| `code_mode_exports_all_tools_metadata_for_builtin_tools` | ALL_TOOLS 包含内置工具的元数据 |
| `code_mode_exports_all_tools_metadata_for_namespaced_mcp_tools` | ALL_TOOLS 包含命名空间 MCP 工具的元数据 |
| `code_mode_can_call_hidden_dynamic_tools` | 可调用隐藏的动态工具并获取正确结果 |
| `code_mode_can_print_content_only_mcp_tool_result_fields` | 打印仅含 content 的 MCP 工具结果 |
| `code_mode_can_print_error_mcp_tool_result_fields` | 打印 MCP 工具错误结果 |
| `code_mode_can_store_and_load_values_across_turns` | 跨轮次使用 store/load 存储和加载值 |
