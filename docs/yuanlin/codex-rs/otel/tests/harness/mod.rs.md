# `harness/mod.rs` — OTEL 测试辅助工具函数

## 功能概述
该文件为 `codex-otel` 集成测试提供共用的辅助函数，包括构建带默认标签的内存 MetricsClient、获取最新指标快照、按名称查找指标、属性转映射、以及直方图数据提取等功能。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无独立结构体 | — | 仅提供辅助函数 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_metrics_with_defaults` | `(default_tags) -> Result<(MetricsClient, InMemoryMetricExporter)>` | 构建带默认标签的内存指标客户端 |
| `latest_metrics` | `(exporter) -> ResourceMetrics` | 获取最新一批导出的指标 |
| `find_metric` | `(resource_metrics, name) -> Option<&Metric>` | 按名称查找指标 |
| `attributes_to_map` | `(attributes) -> BTreeMap<String, String>` | 将 KeyValue 属性转换为排序映射 |
| `histogram_data` | `(resource_metrics, name) -> (Vec<f64>, Vec<u64>, f64, u64)` | 提取直方图的边界、桶计数、总和和计数 |

## 依赖关系
- **引用的 crate**: `codex_otel`, `opentelemetry_sdk`
- **被引用方**: `suite/` 下所有测试模块

## 实现备注
- 使用 `InMemoryMetricExporter` 实现无网络的指标收集测试
