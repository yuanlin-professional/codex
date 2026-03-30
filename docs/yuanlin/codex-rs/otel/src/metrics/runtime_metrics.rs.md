# `runtime_metrics.rs` — 运行时指标摘要聚合

## 功能概述
该文件定义了 `RuntimeMetricTotals` 和 `RuntimeMetricsSummary` 两个结构体，用于从 OTEL `ResourceMetrics` 快照中聚合运行时指标（工具调用、API 调用、SSE 事件、WebSocket 事件以及 Responses API 定时指标）。提供 `from_snapshot` 从快照提取摘要，以及 `merge` 用于跨轮次累加。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `RuntimeMetricTotals` | 结构体 | 单类指标的计数和持续时间总计 |
| `RuntimeMetricsSummary` | 结构体 | 完整的运行时指标摘要，涵盖 13 项指标 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `RuntimeMetricsSummary::from_snapshot` | `(snapshot: &ResourceMetrics) -> Self` | 从快照提取完整摘要 |
| `merge` | `(&mut self, other: Self)` | 合并两个摘要（累加计数/时间，取非零定时值） |
| `responses_api_summary` | `(&self) -> RuntimeMetricsSummary` | 仅保留 Responses API 相关定时指标的子摘要 |

## 依赖关系
- **引用的 crate**: `opentelemetry_sdk`
- **被引用方**: `SessionTelemetry::runtime_metrics_summary`

## 实现备注
- 计数器通过 `Sum` 聚合提取，直方图通过 `Histogram.sum` 提取毫秒值
- `f64_to_u64` 对非有限值和负值返回 0
