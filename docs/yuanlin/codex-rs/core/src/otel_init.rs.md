# `otel_init.rs` — OpenTelemetry 提供者初始化

## 功能概述

此文件从应用配置（`Config`）构建 OpenTelemetry 提供者，支持多种导出器（None、Statsig、OTLP HTTP、OTLP gRPC），并允许为追踪和指标分别配置不同的导出器。当分析功能被禁用时，指标导出器会被设为 None。文件还提供了一个用于过滤 Codex 自有事件的导出过滤器。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| （无独立类型定义） | — | — |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_provider` | `(config, service_version, service_name_override, default_analytics_enabled) -> Result<Option<OtelProvider>>` | 从配置构建 OTel 提供者 |
| `codex_export_filter` | `(meta: &tracing::Metadata) -> bool` | 过滤仅导出 `codex_otel` 模块的事件 |

## 依赖关系

- **引用的 crate**: `codex_otel`（`OtelProvider`、`OtelSettings`）、`codex_features`（Feature 标志）、`crate::config`
- **被引用方**: `lib.rs` 公开导出此模块，应用启动时调用初始化 OTel

## 实现备注

- 导出器类型映射：None -> None、Statsig -> Statsig、OtlpHttp -> OtlpHttp（含协议和 TLS 配置）、OtlpGrpc -> OtlpGrpc
- `analytics_enabled` 控制指标导出器是否生效
- `runtime_metrics` 功能通过 Feature 标志控制
- 服务名称优先使用覆盖值，回退到 `originator().value`
