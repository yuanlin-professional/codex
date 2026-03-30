# `client_tests.rs` — 测试模块

## 测试覆盖范围

测试 `ModelClient` 的辅助功能，包括子代理（subagent）HTTP 头构建、空输入下的 memory 摘要行为，以及认证请求遥测上下文 `AuthRequestTelemetryContext` 的状态追踪。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `build_subagent_headers_sets_other_subagent_label` | 当会话来源为 `SubAgent(Other("memory_consolidation"))` 时，构建的 HTTP 头中 `x-openai-subagent` 值应为 `"memory_consolidation"` |
| `summarize_memories_returns_empty_for_empty_input` | 当传入空的 memory 列表时，`summarize_memories` 应成功返回空结果 |
| `auth_request_telemetry_context_tracks_attached_auth_and_retry_phase` | 认证遥测上下文应正确记录认证模式、认证头是否附加、头名称、是否为未授权重试，以及恢复模式和阶段信息 |
