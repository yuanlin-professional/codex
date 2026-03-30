# `snapshot.rs` — 测试模块

## 测试覆盖范围
该文件测试运行时指标快照功能，验证在不关闭 provider 的情况下通过 `snapshot()` 方法收集指标数据，确保不触发周期性导出。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `snapshot_collects_metrics_without_shutdown` | MetricsClient 快照在不关闭时收集指标 |
| `manager_snapshot_metrics_collects_without_shutdown` | SessionTelemetry 快照在不关闭时收集带元数据标签的指标 |
