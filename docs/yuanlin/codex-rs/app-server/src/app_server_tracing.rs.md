# `app_server_tracing.rs` — 请求级 OpenTelemetry tracing span 构建器

## 功能概述

本模块为 app-server 的所有传输方式（stdio、websocket、in-process）提供统一的 tracing span 构建工具。它将每个入站 JSON-RPC 请求包装为一个 `app_server.request` span，并在其中记录 RPC 方法名、传输类型、连接 ID、客户端名称/版本等元数据。对于携带 W3C Trace Context 的请求，会将其设置为 span 的远程父上下文，从而实现跨服务的分布式追踪关联。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| 无自定义公开结构体 | — | 本模块仅导出函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `request_span` | `(request: &JSONRPCRequest, transport, connection_id, session) -> Span` | 为 JSON-RPC 传输（stdio/ws）的入站请求创建 tracing span |
| `typed_request_span` | `(request: &ClientRequest, connection_id, session) -> Span` | 为 in-process 传输的类型化请求创建 tracing span，transport 固定为 `"in-process"` |
| `transport_name` | `(transport: AppServerTransport) -> &'static str` | 将传输枚举映射为字符串标签 |
| `app_server_request_span_template` | `(method, transport, request_id, connection_id) -> Span` | 构建带有标准 OTel 字段的 `info_span!` 模板 |
| `attach_parent_context` | `(span, method, request_id, parent_trace)` | 将 W3C trace context 或环境变量中的 traceparent 附加为 span 的父上下文 |
| `initialize_client_info` | `(request: &JSONRPCRequest) -> Option<InitializeParams>` | 从 `initialize` 请求中提取客户端信息 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`（`ClientRequest`、`InitializeParams`、`JSONRPCRequest`）、`codex_otel`（W3C trace context 工具）、`codex_protocol`（`W3cTraceContext`）、`tracing`
- **被引用方**: `message_processor` 模块（处理入站请求时调用）、`in_process` 模块

## 实现备注

- `app_server_request_span_template` 使用 `field::Empty` 占位符延迟记录 `client_name`、`client_version` 和 `turn.id`，避免在 span 创建时强制要求所有字段已知。
- 当请求未携带 W3C trace context 时，会尝试从 `TRACEPARENT` 环境变量回退获取父上下文。
