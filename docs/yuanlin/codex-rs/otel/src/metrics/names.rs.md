# `names.rs` — 指标名称常量定义

## 功能概述
该文件定义了 Codex 使用的所有指标名称字符串常量，涵盖工具调用、API 请求、SSE 事件、WebSocket 请求/事件、Responses API 定时、Turn 级别持续时间（TTFT/TTFM/E2E）、Token 使用量、Profile 使用和启动预热等指标。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 全部为 `&str` 常量 | — | 如 `TOOL_CALL_COUNT_METRIC`, `API_CALL_DURATION_METRIC` 等 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| 无函数 | — | 纯常量定义 |

## 依赖关系
- **引用的 crate**: 无
- **被引用方**: `session_telemetry.rs`, `runtime_metrics.rs`

## 实现备注
- 所有指标名称遵循 `codex.` 前缀命名约定
