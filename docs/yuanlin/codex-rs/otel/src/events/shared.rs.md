# `shared.rs` — 遥测事件发射宏和共享工具

## 功能概述
该文件定义了三个内部宏 `log_event!`、`trace_event!` 和 `log_and_trace_event!`，用于向 OTEL 日志和追踪目标发送结构化事件。每个事件自动附加时间戳、conversation ID、应用版本、认证模式、模型等会话元数据字段。`timestamp()` 函数提供毫秒精度的 RFC 3339 时间戳。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无独立结构体 | — | 宏和辅助函数 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `timestamp` | `() -> String` | 返回当前 UTC 时间的 RFC 3339 毫秒格式字符串 |
| `log_event!` | 宏 | 向 `OTEL_LOG_ONLY_TARGET` 发送含元数据的 INFO 事件 |
| `trace_event!` | 宏 | 向 `OTEL_TRACE_SAFE_TARGET` 发送含元数据的 INFO 事件 |
| `log_and_trace_event!` | 宏 | 同时发送日志和追踪事件，支持共用字段+各自独有字段 |

## 依赖关系
- **引用的 crate**: `chrono`, `tracing`
- **被引用方**: `session_telemetry.rs`

## 实现备注
- 日志事件包含 user.account_id 和 user.email，追踪事件不包含
