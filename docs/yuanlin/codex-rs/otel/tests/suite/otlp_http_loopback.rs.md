# `otlp_http_loopback.rs` — 测试模块

## 测试覆盖范围
该文件通过本地 TCP 回环测试验证 OTLP HTTP 导出器能将指标和追踪数据正确发送到收集器端点。包括指标 JSON 导出、追踪 JSON 导出、多线程 tokio 运行时中的追踪导出以及单线程 tokio 运行时中的追踪导出。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `otlp_http_exporter_sends_metrics_to_collector` | OTLP HTTP 指标导出到本地收集器并验证 JSON 内容 |
| `otlp_http_exporter_sends_traces_to_collector` | OTLP HTTP 追踪导出到本地收集器 |
| `otlp_http_exporter_sends_traces_to_collector_in_tokio_runtime` | 在多线程 tokio 运行时中导出追踪 |
| `otlp_http_exporter_sends_traces_to_collector_in_current_thread_tokio_runtime` | 在单线程 tokio 运行时中导出追踪 |
