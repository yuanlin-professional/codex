# `responses.rs` — SSE 流处理和事件解析

## 功能概述

实现 Responses API 的 SSE 事件流处理逻辑。`process_sse` 从字节流解析 SSE 事件并转换为 `ResponseEvent`。`spawn_response_stream` 创建异步任务处理流式响应（含速率限制头、服务模型和推理标记解析）。`stream_from_fixture` 从文件加载 SSE 测试数据。`process_responses_event` 是核心事件分发器，处理 output_item.done、output_text.delta、reasoning_summary_text.delta、response.created/completed/failed/incomplete 等事件类型。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ResponsesStreamEvent` | struct | SSE 事件反序列化结构（kind/headers/response/item/delta 等） |
| `ResponsesEventError` | enum | 事件处理错误（包装 ApiError） |
| `ResponseCompleted` / `ResponseCompletedUsage` | struct | response.completed 事件的解析结构 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `process_sse` | `async fn(ByteStream, tx, idle_timeout, telemetry)` | SSE 流处理主循环 |
| `spawn_response_stream` | `fn(StreamResponse, timeout, telemetry, turn_state) -> ResponseStream` | 启动流处理任务 |
| `process_responses_event` | `fn(event) -> Result<Option<ResponseEvent>>` | 事件类型分发和解析 |
| `stream_from_fixture` | `fn(path, timeout) -> Result<ResponseStream>` | 从文件加载测试流 |

## 依赖关系

- **引用的 crate**: `eventsource_stream`, `codex_protocol::models`, `regex_lite`
- **被引用方**: `endpoint/responses.rs`, `endpoint/responses_websocket.rs`, `lib.rs`

## 实现备注

- `response.failed` 根据错误码映射为不同的 `ApiError` 变体（ContextWindowExceeded、QuotaExceeded、Retryable 等）。
- 速率限制重试延迟从错误消息中通过正则解析 "try again in X s/ms"。
- 包含大量单元测试覆盖各种事件类型和错误场景。
