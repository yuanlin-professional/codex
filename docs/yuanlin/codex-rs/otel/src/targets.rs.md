# `targets.rs` — OTEL 日志/追踪目标前缀定义
该文件定义了 OpenTelemetry 事件导出路由使用的目标（target）字符串常量和过滤函数。`OTEL_LOG_ONLY_TARGET` 用于仅导出到日志的事件，`OTEL_TRACE_SAFE_TARGET` 用于可安全导出到追踪的事件。`is_log_export_target` 和 `is_trace_safe_target` 两个辅助函数基于这些前缀进行过滤判断。
