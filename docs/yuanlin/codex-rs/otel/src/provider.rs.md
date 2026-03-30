# `provider.rs` — OTEL Provider 集成层

## 功能概述
`OtelProvider` 是 Codex OTEL 子系统的顶层集成点，负责根据 `OtelSettings` 创建和管理日志 Provider、追踪 Provider 和指标客户端。提供 tracing_subscriber 兼容的日志和追踪 Layer，以及导出过滤器。在 Drop 时自动关闭所有 Provider。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `OtelProvider` | 结构体 | 持有 logger、tracer_provider、tracer 和 metrics 的顶层提供者 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `OtelProvider::from` | `(settings: &OtelSettings) -> Result<Option<Self>>` | 根据配置创建 Provider（全部禁用时返回 None） |
| `logger_layer` | `(&self) -> Option<impl Layer>` | 获取日志导出 tracing Layer |
| `tracing_layer` | `(&self) -> Option<impl Layer>` | 获取追踪导出 tracing Layer |
| `log_export_filter` | `(meta) -> bool` | 日志导出过滤器（仅 log_only 目标） |
| `trace_export_filter` | `(meta) -> bool` | 追踪导出过滤器（span + trace_safe 目标） |
| `shutdown` | `(&self)` | 刷新并关闭所有 Provider |

## 依赖关系
- **引用的 crate**: `opentelemetry`, `opentelemetry_sdk`, `opentelemetry_otlp`, `tracing_subscriber`, `gethostname`
- **被引用方**: `codex-core` 初始化

## 实现备注
- 日志资源包含 host.name 属性，追踪资源不包含
- 在多线程 tokio 运行时中使用 `TokioBatchSpanProcessor`，否则使用普通 `BatchSpanProcessor`
