# `trace_context.rs` — W3C 追踪上下文工具函数

## 功能概述
该文件提供 W3C Trace Context 的序列化/反序列化和 span 关联功能。支持从当前 span 提取 traceparent/tracestate、从 W3C 格式创建 OpenTelemetry Context、设置 span 父上下文、以及从环境变量 TRACEPARENT/TRACESTATE 加载追踪上下文。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无独立结构体 | — | 纯函数接口 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `current_span_w3c_trace_context` | `() -> Option<W3cTraceContext>` | 从当前 span 提取 W3C 追踪上下文 |
| `span_w3c_trace_context` | `(span: &Span) -> Option<W3cTraceContext>` | 从指定 span 提取 W3C 追踪上下文 |
| `context_from_w3c_trace_context` | `(trace: &W3cTraceContext) -> Option<Context>` | 从 W3C 格式创建 OTel Context |
| `set_parent_from_w3c_trace_context` | `(span, trace) -> bool` | 设置 span 的父追踪上下文 |
| `traceparent_context_from_env` | `() -> Option<Context>` | 从环境变量加载追踪上下文（懒初始化） |

## 依赖关系
- **引用的 crate**: `opentelemetry`, `opentelemetry_sdk`, `tracing_opentelemetry`, `codex_protocol`
- **被引用方**: `codex-core` 追踪集成

## 实现备注
- 环境变量追踪上下文使用 `OnceLock` 全局缓存
- 无效的 traceparent 会发出 tracing warn 并返回 None
