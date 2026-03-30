# `timer.rs` — 指标计时器，自动记录耗时到直方图

## 功能概述
`Timer` 结构体在创建时记录起始时间，在 Drop 时自动将经过时间记录到指定的直方图指标。也可以通过 `record` 方法手动触发记录并附加额外标签。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Timer` | 结构体 | 持有指标名、预设标签、MetricsClient 引用和起始时间 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Timer::new` | `(name, tags, client) -> Self` | 创建计时器，记录起始时间 |
| `Timer::record` | `(&self, additional_tags) -> Result<()>` | 手动记录耗时并附加额外标签 |
| `Drop::drop` | `(&mut self)` | 自动在析构时记录耗时 |

## 依赖关系
- **引用的 crate**: 无外部依赖
- **被引用方**: `MetricsClient::start_timer`, `SessionTelemetry::start_timer`

## 实现备注
- Drop 实现中的错误通过 `tracing::error!` 记录，不会 panic
