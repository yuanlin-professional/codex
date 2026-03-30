# `lib.rs` — codex-otel crate 入口

## 功能概述
该文件是 `codex-otel` crate 的根模块，声明并重导出所有子模块的公共类型。包括 `OtelProvider`、`SessionTelemetry`、`MetricsClient`、`RuntimeMetricsSummary`、`Timer`、W3C trace context 工具函数，以及 `TelemetryAuthMode` 和 `ToolDecisionSource` 枚举。提供 `start_global_timer` 全局便利函数。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolDecisionSource` | 枚举 | 工具审批决策来源：AutomatedReviewer/Config/User |
| `TelemetryAuthMode` | 枚举 | 遥测认证模式：ApiKey/Chatgpt |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_global_timer` | `(name, tags) -> MetricsResult<Timer>` | 使用全局 MetricsClient 启动计时器 |

## 依赖关系
- **引用的 crate**: `codex_app_server_protocol`, `codex_utils_string`, `serde`, `strum_macros`
- **被引用方**: `codex-core`, 各测试模块

## 实现备注
- `TelemetryAuthMode` 实现了从 `codex_app_server_protocol::AuthMode` 的转换
