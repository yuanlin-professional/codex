# `mcp_tool_call_tests.rs` — 测试模块

## 测试覆盖范围

MCP 工具调用的审批流程、权限判断、elicitation 请求构建、持久化审批决策、Guardian 子代理路由、ARC 安全监控集成，以及工具调用 span 追踪字段验证。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `approval_required_when_read_only_false_and_destructive` | 当 read_only 为 false 且 destructive 为 true 时需要审批 |
| `approval_required_when_read_only_false_and_open_world` | 当 read_only 为 false 且 open_world 为 true 时需要审批 |
| `approval_required_when_destructive_even_if_read_only_true` | 即使 read_only 为 true，destructive 为 true 时仍需审批 |
| `approval_required_when_annotations_are_absent` | 当注解缺失时需要审批 |
| `approval_not_required_when_read_only_and_other_hints_are_absent` | 当 read_only 为 true 且无其他提示时不需要审批 |
| `prompt_mode_does_not_allow_persistent_remember` | Prompt 模式下不允许持久化记住决策 |
| `approval_question_text_prepends_safety_reason` | 审批问题文本前置安全原因说明 |
| `mcp_tool_call_span_records_expected_fields` | MCP 工具调用 span 记录预期的追踪字段 |
| `approval_elicitation_request_uses_message_override_and_preserves_tool_params_keys` | elicitation 请求使用消息覆盖并保留工具参数键 |
| `custom_mcp_tool_question_mentions_server_name` | 自定义 MCP 工具问题中提及服务器名称 |
| `codex_apps_tool_question_uses_fallback_app_label` | Codex Apps 工具问题使用回退应用标签 |
| `trusted_codex_apps_tool_question_offers_always_allow` | 受信任的 Codex Apps 工具问题提供始终允许选项 |
| `codex_apps_tool_question_without_elicitation_omits_always_allow` | 无 elicitation 时 Codex Apps 工具问题省略始终允许选项 |
| `custom_mcp_tool_question_offers_session_remember_and_always_allow` | 自定义 MCP 工具问题提供会话记住和始终允许选项 |
| `custom_servers_support_session_and_persistent_approval` | 自定义服务器支持会话和持久化审批 |
| `codex_apps_connectors_support_persistent_approval` | Codex Apps 连接器支持持久化审批 |
| `sanitize_mcp_tool_result_for_model_rewrites_image_content` | 清理 MCP 工具结果时将不支持的图片内容替换为文本 |
| `sanitize_mcp_tool_result_for_model_preserves_image_when_supported` | 当支持图片输入时保留原始图片内容 |
| `mcp_tool_call_request_meta_includes_turn_metadata_for_custom_server` | 自定义服务器的工具调用请求元数据包含 turn 元数据 |
| `codex_apps_tool_call_request_meta_includes_turn_metadata_and_codex_apps_meta` | Codex Apps 工具调用请求元数据包含 turn 元数据和 Codex Apps 元信息 |
| `accepted_elicitation_content_converts_to_request_user_input_response` | 已接受的 elicitation 内容转换为用户输入响应 |
| `approval_elicitation_meta_marks_tool_approvals` | 审批 elicitation 元数据标记工具审批 |
| `approval_elicitation_meta_merges_session_and_always_persist_for_custom_servers` | 审批 elicitation 元数据为自定义服务器合并会话和始终持久化 |
| `guardian_mcp_review_request_includes_invocation_metadata` | Guardian MCP 审查请求包含调用元数据 |
| `guardian_mcp_review_request_includes_annotations_when_present` | Guardian MCP 审查请求在存在注解时包含注解 |
| `prepare_arc_request_action_serializes_mcp_tool_call_shape` | 准备 ARC 请求动作时序列化 MCP 工具调用结构 |
| `guardian_review_decision_maps_to_mcp_tool_decision` | Guardian 审查决策映射到 MCP 工具决策 |
| `approval_elicitation_meta_includes_connector_source_for_codex_apps` | 审批 elicitation 元数据为 Codex Apps 包含连接器来源 |
| `approval_elicitation_meta_merges_session_and_always_persist_with_connector_source` | 审批 elicitation 元数据合并会话/始终持久化与连接器来源 |
| `approval_callsite_mode_distinguishes_default_and_always_allow` | 审批调用点模式区分默认和始终允许 |
| `declined_elicitation_response_stays_decline` | 拒绝的 elicitation 响应保持拒绝状态 |
| `synthetic_decline_request_user_input_response_stays_decline` | 合成拒绝的用户输入响应保持拒绝状态 |
| `accepted_elicitation_response_uses_always_persist_meta` | 接受的 elicitation 响应使用始终持久化元数据 |
| `accepted_elicitation_response_uses_session_persist_meta` | 接受的 elicitation 响应使用会话持久化元数据 |
| `accepted_elicitation_without_content_defaults_to_accept` | 无内容的接受 elicitation 默认为接受 |
| `persist_codex_app_tool_approval_writes_tool_override` | 持久化 Codex App 工具审批写入工具覆盖配置 |
| `persist_custom_mcp_tool_approval_writes_tool_override` | 持久化自定义 MCP 工具审批写入工具覆盖配置 |
| `maybe_persist_mcp_tool_approval_reloads_session_config` | 持久化 MCP 工具审批后重新加载会话配置 |
| `maybe_persist_mcp_tool_approval_reloads_session_config_for_custom_server` | 为自定义服务器持久化后重新加载会话配置 |
| `maybe_persist_mcp_tool_approval_writes_project_config_for_project_server` | 为项目服务器写入项目配置 |
| `approve_mode_skips_when_annotations_do_not_require_approval` | Approve 模式下注解不需要审批时跳过 |
| `guardian_mode_skips_auto_when_annotations_do_not_require_approval` | Guardian 模式下注解不需要审批时自动跳过 |
| `prompt_mode_waits_for_approval_when_annotations_do_not_require_approval` | Prompt 模式下即使注解不需要审批也等待用户确认 |
| `approve_mode_blocks_when_arc_returns_interrupt_for_model` | ARC 返回中断模型时 Approve 模式阻止执行 |
| `custom_approve_mode_blocks_when_arc_returns_interrupt_for_model` | 自定义服务器 ARC 返回中断模型时阻止执行 |
| `approve_mode_blocks_when_arc_returns_interrupt_without_annotations` | 无注解时 ARC 返回中断仍阻止执行 |
| `full_access_mode_skips_arc_monitor_for_all_approval_modes` | 完全访问模式下所有审批模式跳过 ARC 监控 |
| `approve_mode_routes_arc_ask_user_to_guardian_when_guardian_reviewer_is_enabled` | 启用 Guardian 审查者时将 ARC ask-user 路由到 Guardian |
