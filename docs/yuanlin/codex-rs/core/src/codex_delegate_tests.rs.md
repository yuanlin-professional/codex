# `codex_delegate_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖 Codex 委托（delegate）机制的核心功能，包括事件转发（forward_events）、操作转发（forward_ops）、权限请求处理（handle_request_permissions）、执行审批处理（handle_exec_approval）以及 MCP 工具调用的 Guardian 审批流程。主要验证异步通道通信、取消令牌（CancellationToken）行为、trace 上下文传递及审批决策的端到端正确性。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `forward_events_cancelled_while_send_blocked_shuts_down_delegate` | 当发送通道阻塞时取消 forward_events，验证委托能正确关闭并发出 Interrupt 和 Shutdown 操作 |
| `forward_ops_preserves_submission_trace_context` | 验证 forward_ops 在转发提交操作时保留 W3C trace 上下文（traceparent 和 tracestate） |
| `handle_request_permissions_uses_tool_call_id_for_round_trip` | 验证权限请求处理使用 tool_call_id 进行请求-响应的完整往返匹配 |
| `handle_exec_approval_uses_call_id_for_guardian_review_and_approval_id_for_reply` | 验证执行审批处理使用 call_id 发起 Guardian 审查，使用 approval_id 返回审批结果 |
| `delegated_mcp_guardian_abort_returns_synthetic_decline_answer` | 当 Guardian 子代理被取消时，验证 MCP 工具审批请求返回合成的拒绝（decline）应答 |
