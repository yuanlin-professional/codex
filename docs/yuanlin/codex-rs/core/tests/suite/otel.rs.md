# `otel.rs` — 测试模块

## 测试覆盖范围
OpenTelemetry（OTel）遥测事件的发射与记录，包括 API 请求事件、SSE 事件、工具结果、工具决策等多种追踪场景。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `extract_log_field_handles_empty_bare_values` | 日志字段提取器正确处理空裸值 |
| `extract_log_field_does_not_confuse_similar_keys` | 日志字段提取器不会混淆相似键名 |
| `responses_api_emits_api_request_event` | Responses API 发射 codex.api_request 和 codex.conversation_starts 事件 |
| `process_sse_emits_tracing_for_output_item` | SSE 处理为 output_item.done 事件发射追踪日志 |
| `process_sse_emits_failed_event_on_parse_error` | SSE 解析错误时发射带错误信息的追踪事件 |
| `process_sse_records_failed_event_when_stream_closes_without_completed` | 流在未收到 response.completed 前关闭时记录失败事件 |
| `process_sse_failed_event_records_response_error_message` | response.failed 事件中正确记录错误消息 |
| `process_sse_failed_event_logs_parse_error` | response.failed 事件中错误字段解析失败时记录日志 |
| `process_sse_failed_event_logs_missing_error` | response.failed 事件中缺少 error 字段时记录日志 |
| `process_sse_failed_event_logs_response_completed_parse_error` | response.completed 解析失败时记录错误追踪日志 |
| `process_sse_emits_completed_telemetry` | response.completed 事件发射包含 token 计数的遥测数据 |
| `handle_responses_span_records_response_kind_and_tool_name` | handle_responses 追踪 span 记录响应类型和工具名称 |
| `record_responses_sets_span_fields_for_response_events` | 为各种响应事件类型设置正确的 span 字段 |
| `handle_response_item_records_tool_result_for_custom_tool_call` | 自定义工具调用的工具结果记录正确字段 |
| `handle_response_item_records_tool_result_for_function_call` | 函数调用的工具结果记录正确字段 |
| `handle_response_item_records_tool_result_for_local_shell_missing_ids` | 缺少 ID 的 local_shell 调用记录失败的工具结果 |
| `handle_response_item_records_tool_result_for_local_shell_call` | local_shell 调用记录正确的工具结果（仅 macOS） |
| `handle_container_exec_autoapprove_from_config_records_tool_decision` | 配置自动审批时记录 approved/config 工具决策 |
| `handle_container_exec_user_approved_records_tool_decision` | 用户批准时记录 approved/user 工具决策 |
| `handle_container_exec_user_approved_for_session_records_tool_decision` | 用户会话批准时记录 approvedforsession/user 工具决策 |
| `handle_sandbox_error_user_approves_retry_records_tool_decision` | 沙箱错误后用户批准重试时记录 approved/user 工具决策 |
| `handle_container_exec_user_denies_records_tool_decision` | 用户拒绝时记录 denied/user 工具决策 |
| `handle_sandbox_error_user_approves_for_session_records_tool_decision` | 沙箱错误后用户会话批准时记录 approvedforsession/user 工具决策 |
| `handle_sandbox_error_user_denies_records_tool_decision` | 沙箱错误后用户拒绝时记录 denied/user 工具决策 |
