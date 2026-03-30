# `client_websockets.rs` — 测试模块

## 测试覆盖范围

测试模型客户端的 WebSocket 通信功能，包括基础请求流式传输、连接复用(跨轮次/跨会话)、预连接(preconnect)与预热(prewarm)、OpenTelemetry trace 上下文传播、V2 协议(增量请求/previous_response_id)、遥测指标(websocket_calls/events)、运行时时序指标(timing metrics)、推理包含事件、速率限制事件、使用量限制错误、无效请求错误、连接限制自动重连、turn 元数据转发，以及指令变更时的完整重建。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `responses_websocket_streams_request` | 基础 WebSocket 流式请求，验证 response.create 类型、模型名和 beta 头 |
| `responses_websocket_streams_without_feature_flag_when_provider_supports_websockets` | provider 支持 WebSocket 时无需 feature flag 即可使用 |
| `responses_websocket_reuses_connection_with_per_turn_trace_payloads` | 跨轮次复用连接并携带各自的 trace 上下文 |
| `responses_websocket_preconnect_does_not_replace_turn_trace_payload` | 预连接不替换实际轮次的 trace 上下文 |
| `responses_websocket_preconnect_reuses_connection` | 预连接后的请求复用同一连接 |
| `responses_websocket_request_prewarm_reuses_connection` | 请求预热后复用连接，验证 generate=false 和 previous_response_id |
| `responses_websocket_reuses_connection_after_session_drop` | 会话释放后新会话仍复用连接 |
| `responses_websocket_preconnect_is_reused_even_with_header_changes` | 头信息变更后预连接仍被复用 |
| `responses_websocket_request_prewarm_is_reused_even_with_header_changes` | 头信息变更后预热请求仍被复用 |
| `responses_websocket_prewarm_uses_v2_when_provider_supports_websockets` | provider 支持时预热使用 V2 协议 |
| `responses_websocket_preconnect_runs_when_only_v2_feature_enabled` | 仅启用 V2 功能时预连接正常运行 |
| `responses_websocket_v2_requests_use_v2_when_provider_supports_websockets` | V2 请求使用增量输入和 previous_response_id |
| `responses_websocket_v2_incremental_requests_are_reused_across_turns` | V2 增量请求跨轮次复用 |
| `responses_websocket_v2_wins_when_both_features_enabled` | V1 和 V2 同时启用时 V2 优先 |
| `responses_websocket_emits_websocket_telemetry_events` | 验证 WebSocket 调用和事件的遥测指标 |
| `responses_websocket_includes_timing_metrics_header_when_runtime_metrics_enabled` | 启用运行时指标时包含时序指标头并记录各项时序数据 |
| `responses_websocket_omits_timing_metrics_header_when_runtime_metrics_disabled` | 禁用运行时指标时不包含时序指标头 |
| `responses_websocket_emits_reasoning_included_event` | 服务端返回 X-Reasoning-Included 头时触发相应事件 |
| `responses_websocket_emits_rate_limit_events` | 速率限制事件包含 plan_type、primary window、credits 等完整信息 |
| `responses_websocket_usage_limit_error_emits_rate_limit_event` | 429 使用量限制错误触发速率限制和错误事件 |
| `responses_websocket_invalid_request_error_with_status_is_forwarded` | 400 无效请求错误(如不支持图片输入)被正确转发 |
| `responses_websocket_connection_limit_error_reconnects_and_completes` | 连接限制错误后自动重连并完成请求 |
| `responses_websocket_uses_incremental_create_on_prefix` | 输入为前缀时使用增量 create 请求 |
| `responses_websocket_forwards_turn_metadata_on_initial_and_incremental_create` | 初始和增量请求均转发 turn 元数据 |
| `responses_websocket_uses_previous_response_id_when_prefix_after_completed` | 完成后输入为前缀时使用 previous_response_id |
| `responses_websocket_creates_on_non_prefix` | 输入非前缀时发送完整 create 请求 |
| `responses_websocket_creates_when_non_input_request_fields_change` | 非输入字段(如 instructions)变更时发送完整请求 |
| `responses_websocket_v2_creates_with_previous_response_id_on_prefix` | V2 模式下前缀输入使用 previous_response_id |
| `responses_websocket_v2_creates_without_previous_response_id_when_non_input_fields_change` | V2 模式下非输入字段变更时不使用 previous_response_id |
| `responses_websocket_v2_after_error_uses_full_create_without_previous_response_id` | V2 模式下错误后使用完整 create 且不带 previous_response_id |
| `responses_websocket_v2_surfaces_terminal_error_without_close_handshake` | V2 模式下终端错误无需 close 握手即可被捕获 |
| `responses_websocket_v2_sets_openai_beta_header` | V2 模式下正确设置 OpenAI-Beta 头 |
