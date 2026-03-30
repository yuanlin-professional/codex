# `common.rs` — API 通用数据类型和请求结构

## 功能概述

定义 codex-api 全局通用的数据类型，包括压缩输入（`CompactionInput`）、记忆摘要输入/输出（`MemorySummarizeInput`/`MemorySummarizeOutput`）、Responses API 请求结构（`ResponsesApiRequest`、`ResponseCreateWsRequest`）、推理控制（`Reasoning`）、文本控制（`TextControls`/`TextFormat`/`OpenAiVerbosity`）和响应事件流（`ResponseEvent`/`ResponseStream`）。还包含 WebSocket 请求包装和 trace context 注入逻辑。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ResponsesApiRequest` | struct | Responses API 完整请求体 |
| `ResponseCreateWsRequest` | struct | WebSocket 版 response.create 请求体 |
| `ResponseEvent` | enum | 流式响应事件：Created/OutputItemDone/Completed/TextDelta/RateLimits 等 |
| `ResponseStream` | struct | 基于 mpsc 的事件流，实现 `Stream` trait |
| `Reasoning` | struct | 推理控制参数（effort/summary） |
| `TextControls` | struct | 文本输出控制（verbosity/format） |
| `CompactionInput` | struct | 历史压缩请求体 |
| `MemorySummarizeInput` / `MemorySummarizeOutput` | struct | 记忆摘要输入和输出 |

## 依赖关系

- **引用的 crate**: `codex_protocol`, `serde`, `tokio::sync::mpsc`, `futures`
- **被引用方**: 几乎所有 codex-api endpoint 模块

## 实现备注

- `ResponseStream` 通过实现 `futures::Stream` trait 从 mpsc receiver 转换为流。
- `response_create_client_metadata` 将 W3C trace context 注入 WebSocket client_metadata。
