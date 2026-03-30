# `tracing.rs` — 测试用 OpenTelemetry tracing 安装工具

提供 `install_test_tracing()` 函数，用于在集成测试中安装 OpenTelemetry tracing 基础设施。该函数设置 `TraceContextPropagator` 作为全局传播器，创建 `SdkTracerProvider` 和 tracer，并通过 `tracing_subscriber` 注册为默认的 tracing 订阅者。返回的 `TestTracingContext` 持有 provider 和 guard 的生命周期，确保在作用域结束时自动清理。
