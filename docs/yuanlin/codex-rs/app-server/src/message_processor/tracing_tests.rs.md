# `message_processor/tracing_tests.rs` — 测试模块

## 测试覆盖范围

本文件测试 `MessageProcessor` 的 OpenTelemetry tracing span 导出行为，验证 JSON-RPC 请求的 server span 创建、W3C trace context 远程父 span 关联、以及 turn/start 请求的 core turn span 父子关系链。使用内存 span 导出器（`InMemorySpanExporter`）捕获 span 数据进行断言。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `thread_start_jsonrpc_span_exports_server_span_and_parents_children` | thread/start 请求导出 server span，并在携带 remote trace 时正确设置父 span |
| `turn_start_jsonrpc_span_parents_core_turn_spans` | turn/start 请求的 server span 成为 core turn span（codex.op=user_input）的祖先 |
