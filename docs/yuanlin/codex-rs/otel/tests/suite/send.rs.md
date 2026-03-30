# `send.rs` — 测试模块

## 测试覆盖范围
该文件测试指标发送和标签合并逻辑，验证计数器和直方图能正确携带默认标签和调用标签，以及每行标签覆盖默认标签的优先级。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `send_builds_payload_with_tags_and_histograms` | 计数器/直方图正确携带默认+调用标签 |
| `send_merges_default_tags_per_line` | 每行独立合并默认标签，调用标签覆盖同名默认 |
| `client_sends_enqueued_metric` | 后台工作线程正确投递排队的指标 |
| `shutdown_flushes_in_memory_exporter` | 关闭时刷新内存导出器 |
| `shutdown_without_metrics_exports_nothing` | 无指标时关闭不产生导出 |
