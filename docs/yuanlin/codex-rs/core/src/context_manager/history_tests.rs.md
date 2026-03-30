# `history_tests.rs` — 测试模块

## 测试覆盖范围

针对 `history.rs` 中的 `ContextManager` 进行全面测试，覆盖消息过滤、token 用量估算、历史规范化、轮次回滚、图片处理、工具输出截断以及内联图片 token 估算等核心功能。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `filters_non_api_messages` | 验证 system 消息和 `Other` 类型被过滤，user/assistant/reasoning 消息被保留 |
| `non_last_reasoning_tokens_return_zero_when_no_user_messages` | 验证无用户消息时，非最后一段推理 token 计数为零 |
| `non_last_reasoning_tokens_ignore_entries_after_last_user` | 验证仅计算最后一个用户消息之前的推理 token |
| `items_after_last_model_generated_tokens_include_user_and_tool_output` | 验证最后一个模型生成条目之后的用户消息和工具输出被正确计算 |
| `items_after_last_model_generated_tokens_are_zero_without_model_generated_items` | 验证没有模型生成条目时，尾部 token 计数为零 |
| `inter_agent_assistant_messages_are_turn_boundaries` | 验证 inter-agent 助手消息被识别为轮次边界 |
| `for_prompt_preserves_inter_agent_assistant_messages` | 验证 inter-agent 消息在 for_prompt 中被保留 |
| `drop_last_n_user_turns_treats_inter_agent_assistant_messages_as_instruction_turns` | 验证回滚将 inter-agent 消息视为指令轮次 |
| `legacy_inter_agent_assistant_messages_are_not_turn_boundaries` | 验证旧格式的 inter-agent 消息不被识别为轮次边界 |
| `total_token_usage_includes_all_items_after_last_model_generated_item` | 验证总 token 计数包含最近 API 返回值加上新增条目 |
| `for_prompt_strips_images_when_model_does_not_support_images` | 验证不支持图片的模型下，图片被替换为占位文本 |
| `for_prompt_preserves_image_generation_calls_when_images_are_supported` | 验证支持图片时，图片生成调用被保留 |
| `for_prompt_clears_image_generation_result_when_images_are_unsupported` | 验证不支持图片时，图片生成结果被清空 |
| `get_history_for_prompt_drops_ghost_commits` | 验证 GhostSnapshot 条目在 for_prompt 中被移除 |
| `estimate_token_count_with_base_instructions_uses_provided_text` | 验证 token 估算正确使用提供的 base_instructions 文本 |
| `remove_first_item_removes_matching_output_for_function_call` | 验证移除 FunctionCall 时同时移除对应的 FunctionCallOutput |
| `remove_first_item_removes_matching_call_for_output` | 验证移除 FunctionCallOutput 时同时移除对应的 FunctionCall |
| `remove_last_item_removes_matching_call_for_output` | 验证从末尾移除 FunctionCallOutput 时同时移除对应的 FunctionCall |
| `replace_last_turn_images_replaces_tool_output_images` | 验证工具输出中的图片被替换为指定占位文本 |
| `replace_last_turn_images_does_not_touch_user_images` | 验证用户消息中的图片不被替换 |
| `remove_first_item_handles_local_shell_pair` | 验证 LocalShellCall 与 FunctionCallOutput 的配对移除 |
| `drop_last_n_user_turns_preserves_prefix` | 验证回滚保留会话前缀条目 |
| `drop_last_n_user_turns_ignores_session_prefix_user_messages` | 验证回滚跳过上下文类用户消息（environment_context、AGENTS.md、skill、shell_command 等） |
| `drop_last_n_user_turns_trims_context_updates_above_rolled_back_turn` | 验证回滚时修剪轮次前的上下文更新消息，但保留非上下文 developer 消息，并保持 reference_context_item |
| `drop_last_n_user_turns_clears_reference_context_for_mixed_developer_context_bundles` | 验证回滚混合初始上下文包时清除 reference_context_item |
| `remove_first_item_handles_custom_tool_pair` | 验证 CustomToolCall 与 CustomToolCallOutput 的配对移除 |
| `normalization_retains_local_shell_outputs` | 验证规范化保留 LocalShellCall 的输出 |
| `record_items_truncates_function_call_output_content` | 验证记录时对过长的 FunctionCallOutput 进行截断 |
| `record_items_truncates_custom_tool_call_output_content` | 验证记录时对过长的 CustomToolCallOutput 进行截断 |
| `record_items_respects_custom_token_limit` | 验证自定义 token 限制被正确执行 |
| `format_exec_output_truncates_large_error` | 验证大型错误输出被正确截断并标记 |
| `format_exec_output_marks_byte_truncation_without_omitted_lines` | 验证字节截断时不显示行省略标记 |
| `format_exec_output_returns_original_when_within_limits` | 验证在限制内的输出不被截断 |
| `format_exec_output_reports_omitted_lines_and_keeps_head_and_tail` | 验证行截断时保留头部和尾部行 |
| `format_exec_output_prefers_line_marker_when_both_limits_exceeded` | 验证同时超出字节和行限制时优先使用行标记 |
| `normalize_adds_missing_output_for_function_call` | 验证规范化为缺失输出的 FunctionCall 插入 "aborted" 输出 |
| `normalize_adds_missing_output_for_custom_tool_call` | 验证规范化为缺失输出的 CustomToolCall 插入 "aborted" 输出 |
| `normalize_adds_missing_output_for_local_shell_call_with_id` | 验证规范化为缺失输出的 LocalShellCall 插入输出 |
| `normalize_removes_orphan_function_call_output` | 验证规范化移除无对应调用的 FunctionCallOutput |
| `normalize_removes_orphan_custom_tool_call_output` | 验证规范化移除无对应调用的 CustomToolCallOutput |
| `normalize_mixed_inserts_and_removals` | 验证规范化在混合场景下正确插入缺失输出并移除孤立输出 |
| `normalize_adds_missing_output_for_tool_search_call` | 验证规范化为缺失输出的 ToolSearchCall 插入空工具列表输出 |
| `normalize_removes_orphan_client_tool_search_output` | 验证规范化移除客户端类型的孤立 ToolSearchOutput |
| `normalize_keeps_server_tool_search_output_without_matching_call` | 验证服务器类型的 ToolSearchOutput 即使无匹配调用也被保留 |
| `image_data_url_payload_does_not_dominate_message_estimate` | 验证 base64 图片数据不会主导消息的 token 估算 |
| `image_data_url_payload_does_not_dominate_function_call_output_estimate` | 验证 FunctionCallOutput 中的图片估算使用固定成本 |
| `image_data_url_payload_does_not_dominate_custom_tool_call_output_estimate` | 验证 CustomToolCallOutput 中的图片估算使用固定成本 |
| `non_base64_image_urls_are_unchanged` | 验证非 base64 的图片 URL 使用原始序列化长度 |
| `data_url_without_base64_marker_is_unchanged` | 验证无 base64 标记的 data URL 使用原始长度 |
| `non_image_base64_data_url_is_unchanged` | 验证非图片类型的 base64 data URL 使用原始长度 |
| `mixed_case_data_url_markers_are_adjusted` | 验证大小写混合的 data URL 标记被正确识别 |
| `multiple_inline_images_apply_multiple_fixed_costs` | 验证多张内联图片各自应用独立的固定成本 |
| `original_detail_images_scale_with_dimensions` | 验证 `detail: "original"` 的 PNG 图片按实际尺寸的 32px patch 计算 |
| `original_detail_webp_images_scale_with_dimensions` | 验证 `detail: "original"` 的 WebP 图片同样按 patch 计算 |
| `text_only_items_unchanged` | 验证纯文本条目的估算等于原始序列化长度 |
| debug 模式 panic 测试（多个） | 验证在 debug 模式下缺失输出和孤立输出触发 panic |
