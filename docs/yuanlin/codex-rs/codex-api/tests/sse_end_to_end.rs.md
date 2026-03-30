# `sse_end_to_end.rs` — 测试模块

## 测试覆盖范围

验证 `ResponsesClient` 通过 HTTP SSE 传输的端到端流式响应处理。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `responses_stream_parses_items_and_completed_end_to_end` | 完整 SSE 流（两个 output_item.done + response.completed）的解析和事件收集 |
