# `session_telemetry.rs` — 会话遥测事件记录器

## 功能概述
`SessionTelemetry` 是 Codex 的会话级遥测核心，负责记录会话生命周期中的各类事件（用户提示、API 请求、工具调用、SSE/WebSocket 事件、认证恢复等）到 OTEL 日志和追踪系统，并通过 MetricsClient 发送指标。实现了敏感数据分流：完整数据发送到日志，仅统计信息发送到追踪。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SessionTelemetry` | 结构体 | 会话遥测管理器，持有元数据和可选的 MetricsClient |
| `SessionTelemetryMetadata` | 结构体 | 会话元数据：conversation_id、auth_mode、model、originator 等 |
| `AuthEnvTelemetryMetadata` | 结构体 | 认证环境元数据：API key 存在状态、provider key 信息等 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `SessionTelemetry::new` | `(conversation_id, model, ...) -> Self` | 创建会话遥测管理器 |
| `conversation_starts` | `(&self, provider, reasoning_effort, ...)` | 记录会话开始事件 |
| `record_api_request` | `(&self, attempt, status, error, duration, ...)` | 记录 API 请求事件和指标 |
| `user_prompt` | `(&self, items: &[UserInput])` | 记录用户提示（日志含内容，追踪仅含统计） |
| `tool_result_with_tags` | `(&self, tool_name, call_id, ...)` | 记录工具结果事件和指标 |
| `log_sse_event` | `(&self, response, duration)` | 记录 SSE 事件 |
| `record_websocket_event` | `(&self, result, duration)` | 记录 WebSocket 事件 |
| `runtime_metrics_summary` | `(&self) -> Option<RuntimeMetricsSummary>` | 收集运行时指标摘要 |

## 依赖关系
- **引用的 crate**: `codex_api`, `codex_protocol`, `eventsource_stream`, `tokio_tungstenite`
- **被引用方**: `codex-core` 会话循环

## 实现备注
- 使用 `log_and_trace_event!` 宏同时发送日志和追踪事件
- WebSocket timing 指标从 `responsesapi.websocket_timing` 事件中提取
- 指标发送失败不会中断主流程，仅记录 tracing::warn
