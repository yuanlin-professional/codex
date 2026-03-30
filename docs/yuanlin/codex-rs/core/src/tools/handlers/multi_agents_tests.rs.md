# `multi_agents_tests.rs` — 测试模块

## 测试覆盖范围
覆盖 V1（`multi_agents`）和 V2（`multi_agents_v2`）两套多代理协作工具的完整功能，包括 spawn_agent、send_input、wait_agent、close_agent、resume_agent（V1）以及 assign_task、send_message、list_agents（V2）等操作。同时测试了配置构建函数 `build_agent_spawn_config` 和 `build_agent_resume_config` 的行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `handler_rejects_non_function_payloads` | 验证处理器拒绝非 Function 类型的载荷 |
| `spawn_agent_rejects_empty_message` | 验证生成代理时空消息被拒绝 |
| `spawn_agent_rejects_when_message_and_items_are_both_set` | 验证同时提供 message 和 items 时报错 |
| `spawn_agent_uses_explorer_role_and_preserves_approval_policy` | 验证生成代理时应用角色配置并保留审批策略 |
| `spawn_agent_returns_agent_id_without_task_name` | 验证 V1 spawn 返回 agent_id 而非 task_name |
| `multi_agent_v2_spawn_requires_task_name` | 验证 V2 spawn 要求提供 task_name |
| `spawn_agent_errors_when_manager_dropped` | 验证线程管理器已释放时生成代理报错 |
| `multi_agent_v2_spawn_returns_path_and_send_message_accepts_relative_path` | 验证 V2 spawn 返回路径且 send_message 接受相对路径 |
| `multi_agent_v2_send_message_accepts_root_target_from_child` | 验证子代理可以向根代理发送消息 |
| `multi_agent_v2_list_agents_returns_completed_status_and_last_task_message` | 验证 list_agents 返回已完成状态和最后任务消息 |
| `multi_agent_v2_list_agents_filters_by_relative_path_prefix` | 验证 list_agents 按相对路径前缀过滤 |
| `multi_agent_v2_list_agents_omits_closed_agents` | 验证 list_agents 不包含已关闭的代理 |
| `multi_agent_v2_send_message_rejects_structured_items` | 验证 V2 send_message 拒绝非文本内容项 |
| `multi_agent_v2_send_message_interrupts_busy_child_without_triggering_turn` | 验证 send_message 中断忙碌子代理但不触发新轮次 |
| `multi_agent_v2_assign_task_interrupts_busy_child_without_losing_message` | 验证 assign_task 中断忙碌子代理且消息不丢失 |
| `multi_agent_v2_spawn_includes_agent_id_key_when_named` | 验证 V2 命名 spawn 包含 agent_id 键 |
| `multi_agent_v2_spawn_surfaces_task_name_validation_errors` | 验证无效 task_name 时表面化验证错误 |
| `spawn_agent_reapplies_runtime_sandbox_after_role_config` | 验证角色配置后重新应用运行时沙箱策略 |
| `spawn_agent_rejects_when_depth_limit_exceeded` | 验证代理深度超限时拒绝生成 |
| `spawn_agent_allows_depth_up_to_configured_max_depth` | 验证深度在允许范围内可正常生成 |
| `send_input_rejects_empty_message` | 验证发送空消息被拒绝 |
| `send_input_rejects_when_message_and_items_are_both_set` | 验证 send_input 同时提供 message 和 items 时报错 |
| `send_input_rejects_invalid_id` | 验证无效代理 ID 被拒绝 |
| `send_input_reports_missing_agent` | 验证向不存在的代理发送输入时报告缺失 |
| `send_input_interrupts_before_prompt` | 验证中断标志在发送提示之前执行 |
| `send_input_accepts_structured_items` | 验证 send_input 接受结构化输入项 |
| `resume_agent_rejects_invalid_id` | 验证恢复代理时无效 ID 被拒绝 |
| `resume_agent_reports_missing_agent` | 验证恢复不存在的代理时报告缺失 |
| `resume_agent_noops_for_active_agent` | 验证对活跃代理的恢复操作为空操作 |
| `resume_agent_restores_closed_agent_and_accepts_send_input` | 验证恢复已关闭代理后可正常发送输入 |
| `resume_agent_rejects_when_depth_limit_exceeded` | 验证恢复代理时深度超限被拒绝 |
| `wait_agent_rejects_non_positive_timeout` | 验证非正超时值被拒绝 |
| `wait_agent_rejects_invalid_target` | 验证无效目标被拒绝 |
| `wait_agent_rejects_empty_targets` | 验证空目标列表被拒绝 |
| `multi_agent_v2_wait_agent_accepts_targets_argument` | 验证 V2 wait 接受 targets 参数 |
| `wait_agent_returns_not_found_for_missing_agents` | 验证等待不存在的代理返回 NotFound |
| `wait_agent_times_out_when_status_is_not_final` | 验证代理状态非终态时等待超时 |
| `wait_agent_clamps_short_timeouts_to_minimum` | 验证过短的超时被钳制到最小值 |
| `wait_agent_returns_final_status_without_timeout` | 验证代理已完成时立即返回终态 |
| `multi_agent_v2_wait_agent_returns_summary_for_named_targets` | 验证 V2 wait 对命名目标返回摘要 |
| `multi_agent_v2_wait_agent_does_not_return_completed_content` | 验证 V2 wait 不返回已完成的内容 |
| `multi_agent_v2_close_agent_accepts_task_name_target` | 验证 V2 close 接受 task_name 作为目标 |
| `multi_agent_v2_close_agent_rejects_root_target_and_id` | 验证 V2 close 拒绝根目标和原始 ID |
| `close_agent_submits_shutdown_and_returns_previous_status` | 验证关闭代理提交关闭请求并返回先前状态 |
| `tool_handlers_cascade_close_and_resume_and_keep_explicitly_closed_subtrees_closed` | 验证级联关闭和恢复时显式关闭的子树保持关闭 |
| `build_agent_spawn_config_uses_turn_context_values` | 验证生成配置使用 turn 上下文的值 |
| `build_agent_spawn_config_preserves_base_user_instructions` | 验证生成配置保留 base user instructions |
| `build_agent_resume_config_clears_base_instructions` | 验证恢复配置清除 base instructions |
