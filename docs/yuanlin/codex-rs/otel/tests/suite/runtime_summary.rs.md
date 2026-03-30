# `runtime_summary.rs` — 测试模块

## 测试覆盖范围
该文件测试运行时指标摘要收集功能，验证工具调用、API 请求、SSE 事件、WebSocket 连接/事件以及 Responses API 定时指标能被正确汇总到 `RuntimeMetricsSummary` 中。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `runtime_metrics_summary_collects_tool_api_and_streaming_metrics` | 收集工具调用、API 调用、SSE/WebSocket 事件和 Responses API 定时指标的完整摘要 |
