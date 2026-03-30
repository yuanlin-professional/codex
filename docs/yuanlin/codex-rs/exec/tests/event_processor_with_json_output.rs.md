# `event_processor_with_json_output.rs` — 测试模块

## 测试覆盖范围

全面测试 `EventProcessorWithJsonOutput` 的事件映射逻辑，覆盖所有 item 类型的 started/completed 生命周期、ID 映射一致性、todo 列表管理、token 用量传递、错误处理和最终消息恢复。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `map_todo_items_preserves_text_and_completion_state` | TurnPlanStep 到 TodoItem 的映射保留文本和完成状态 |
| `session_configured_produces_thread_started_event` | SessionConfigured 产生 ThreadStarted 事件 |
| `turn_started_emits_turn_started_event` | TurnStarted 通知正确映射 |
| `command_execution_started_and_completed_translate_to_thread_events` | 命令执行 started/completed 生命周期完整映射 |
| `empty_reasoning_items_are_ignored` | 空摘要的推理项被忽略 |
| `unsupported_items_do_not_consume_synthetic_ids` | 不支持的 item 类型不消耗自增 ID |
| `reasoning_items_emit_summary_not_raw_content` | 推理项输出摘要而非原始内容 |
| `web_search_completion_preserves_query_and_action` | Web 搜索完成保留 query 和 action |
| `web_search_start_and_completion_reuse_item_id` | Web 搜索 started/completed 复用相同 item ID |
| `mcp_tool_call_begin_and_end_emit_item_events` | MCP 工具调用 started/completed 完整映射 |
| `mcp_tool_call_failure_sets_failed_status` | MCP 工具调用失败正确设置 Failed 状态 |
| `mcp_tool_call_defaults_arguments_and_preserves_structured_content` | MCP 调用保留 structured_content |
| `collab_spawn_begin_and_end_emit_item_events` | 协作工具 SpawnAgent 的 started/completed 映射 |
| `file_change_completion_maps_change_kinds` | 文件变更类型正确映射（Add/Delete/Update） |
| `file_change_declined_maps_to_failed_status` | 被拒绝的文件变更映射为 Failed 状态 |
| `agent_message_item_updates_final_message` | AgentMessage 完成时更新 final_message |
| `agent_message_item_started_is_ignored` | AgentMessage started 事件被忽略 |
| `reasoning_item_completed_uses_synthetic_id` | 推理项完成使用合成 ID |
| `warning_event_produces_error_item` | 警告产生 Error item |
| `plan_update_emits_started_then_updated_then_completed` | 计划更新完整生命周期 |
| `plan_update_after_completion_starts_new_todo_list_with_new_id` | 完成后新计划使用新 ID |
| `token_usage_update_is_emitted_on_turn_completion` | token 用量在 turn 完成时输出 |
| `turn_completion_recovers_final_message_from_turn_items` | turn 完成时从 items 恢复最终消息 |
| `turn_completion_reconciles_started_items_from_turn_items` | turn 完成时回收未完成的 started items |
| `turn_completion_overwrites_stale_final_message_from_turn_items` | turn 完成时覆盖过期的最终消息 |
| `turn_completion_preserves_streamed_final_message_when_turn_items_are_empty` | items 为空时保留流式最终消息 |
| `failed_turn_clears_stale_final_message` | 失败的 turn 清除过期最终消息 |
| `turn_completion_falls_back_to_final_plan_text` | 无 AgentMessage 时回退到 Plan text |
| `turn_failure_prefers_structured_error_message` | turn 失败优先使用结构化错误信息 |
| `model_reroute_surfaces_as_error_item` | 模型重路由事件表面化为 Error item |
