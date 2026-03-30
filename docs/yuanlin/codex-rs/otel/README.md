# otel

## 功能概述

`codex-otel` 是 Codex 项目的 OpenTelemetry 集成模块，提供统一的可观测性基础设施，包括分布式追踪（Tracing）、指标（Metrics）和日志（Logging）三大遥测信号的采集与导出。它封装了 OpenTelemetry SDK 的初始化配置、OTLP 导出器管理以及 W3C Trace Context 传播等功能。

## 架构说明

该 crate 由以下核心子模块构成：

1. **`provider`（OtelProvider）** - 统一的遥测提供者，负责初始化和管理 OpenTelemetry SDK 的 TracerProvider、MeterProvider 和 LoggerProvider。
2. **`metrics`** - 指标子系统，包括：
   - `runtime_metrics` - 运行时指标摘要（RuntimeMetricsSummary、RuntimeMetricTotals）
   - `timer` - 指标计时器，用于测量操作耗时
   - 全局指标客户端（通过 `global()` 访问）
3. **`events`** - 事件/会话遥测（SessionTelemetry），收集认证环境、会话元数据等遥测信息。
4. **`trace_context`** - W3C Trace Context 传播工具，支持 traceparent 头的解析、设置和提取。
5. **`config`** - OTLP 导出配置。
6. **`otlp`** - OTLP 协议传输层实现。
7. **`targets`** - 日志过滤目标配置。

## 目录结构

```
otel/
  Cargo.toml
  src/
    lib.rs              # 模块入口，导出核心类型和函数
    config.rs           # 导出配置
    events/             # 事件与会话遥测
      session_telemetry.rs
    metrics/            # 指标子系统
      runtime_metrics.rs
      timer.rs
    provider.rs         # OtelProvider 初始化
    trace_context.rs    # W3C Trace Context 工具
    otlp.rs             # OTLP 传输层
    targets.rs          # 日志过滤
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `opentelemetry` | OpenTelemetry 核心 API（logs、metrics、trace） |
| `opentelemetry-otlp` | OTLP 导出器（gRPC/HTTP） |
| `opentelemetry_sdk` | OpenTelemetry SDK 实现 |
| `opentelemetry-appender-tracing` | 将 tracing 日志桥接到 OTel Logs |
| `opentelemetry-semantic-conventions` | 语义约定常量 |
| `tracing` / `tracing-opentelemetry` / `tracing-subscriber` | Rust tracing 生态集成 |
| `codex-protocol` | Codex 协议定义 |
| `codex-api` | Codex API 类型 |
| `codex-app-server-protocol` | AuthMode 等类型（用于 TelemetryAuthMode 转换） |
| `reqwest` | HTTP 客户端（阻塞+异步） |
| `serde` / `serde_json` | 序列化 |
| `tokio` | 异步运行时 |

### Feature Flags

- **`disable-default-metrics-exporter`** - 禁用内置默认指标导出器，供测试使用，防止单元测试尝试网络导出。

## 核心接口/API

### 主要导出

- **`OtelProvider`** - OpenTelemetry 提供者，统一管理追踪/指标/日志。
- **`SessionTelemetry`** / **`SessionTelemetryMetadata`** / **`AuthEnvTelemetryMetadata`** - 会话遥测数据和元数据。
- **`RuntimeMetricTotals`** / **`RuntimeMetricsSummary`** - 运行时指标汇总。
- **`Timer`** - 指标计时器。
- **`ToolDecisionSource`** - 工具决策来源枚举（`AutomatedReviewer` / `Config` / `User`）。
- **`TelemetryAuthMode`** - 遥测认证模式（`ApiKey` / `Chatgpt`）。

### Trace Context 函数

- `context_from_w3c_trace_context()` - 从 W3C traceparent 解析上下文。
- `current_span_trace_id()` / `current_span_w3c_trace_context()` - 获取当前 span 的 trace ID 和 W3C 上下文。
- `set_parent_from_context()` / `set_parent_from_w3c_trace_context()` - 设置父上下文。
- `traceparent_context_from_env()` - 从环境变量获取 traceparent 上下文。

### 全局指标

- `start_global_timer(name, tags) -> MetricsResult<Timer>` - 使用全局指标客户端启动计时器。
