# `metrics/mod.rs` — 度量子系统模块入口与全局 MetricsClient 管理
该文件是 `codex-otel` 度量子系统的模块入口，声明并重导出 `MetricsClient`、`MetricsConfig`、`MetricsExporter`、`MetricsError` 和 `Result` 类型。同时提供全局 `MetricsClient` 的安装（`install_global`）和获取（`global`）功能，使用 `OnceLock` 确保单例语义。
