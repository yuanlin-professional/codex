# `otel_export_routing_policy.rs` — 测试模块

## 测试覆盖范围
该文件测试 OTEL 事件导出路由策略，验证敏感数据（如用户提示、工具参数/输出）仅路由到日志导出而非追踪导出，同时非敏感元数据（如长度、计数）正确路由到追踪导出。覆盖用户提示、工具结果、认证恢复、API 请求和 WebSocket 连接等事件场景。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `otel_export_routing_policy_routes_user_prompt_log_and_trace_events` | 用户提示内容仅路由到日志，长度/计数信息路由到追踪 |
| `otel_export_routing_policy_routes_tool_result_log_and_trace_events` | 工具参数/输出路由到日志，长度信息路由到追踪 |
| `otel_export_routing_policy_routes_auth_recovery_log_and_trace_events` | 认证恢复事件同时路由到日志和追踪 |
| `otel_export_routing_policy_routes_api_request_auth_observability` | API 请求认证信息正确路由 |
| `otel_export_routing_policy_routes_websocket_connect_auth_observability` | WebSocket 连接认证信息正确路由 |
| `otel_export_routing_policy_routes_websocket_request_transport_observability` | WebSocket 请求传输信息正确路由 |
