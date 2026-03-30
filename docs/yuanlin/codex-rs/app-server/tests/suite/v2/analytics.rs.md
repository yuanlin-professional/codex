# `analytics.rs` — 测试模块

## 测试覆盖范围
测试 app-server 的分析指标（analytics/metrics）在 OpenTelemetry 配置下的启用/禁用行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `app_server_default_analytics_disabled_without_flag` | 配置中未设置 analytics 且默认标志为 false 时，指标被禁用 |
| `app_server_default_analytics_enabled_with_flag` | 配置中未设置 analytics 且默认标志为 true 时，指标被启用 |
