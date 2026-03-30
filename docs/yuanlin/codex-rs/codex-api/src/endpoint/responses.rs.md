# `responses.rs` — Responses HTTP SSE 客户端

## 功能概述

`ResponsesClient` 提供基于 HTTP SSE 的 Responses API 调用，发送 POST 请求到 `responses` 端点并返回流式事件。支持对话 ID 和子代理头注入、Azure 端点的 item ID 附加、请求压缩（Zstd）和 turn state 传递。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ResponsesClient<T, A>` | struct | HTTP SSE Responses 客户端 |
| `ResponsesOptions` | struct | 请求选项（conversation_id/session_source/headers/compression/turn_state） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `stream_request` | `async fn(&self, request, options) -> Result<ResponseStream>` | 结构化请求入口 |
| `stream` | `async fn(&self, body, headers, compression, turn_state) -> Result<ResponseStream>` | 原始 JSON body 入口 |

## 依赖关系

- **引用的 crate**: `codex_client`, `codex_protocol`
- **被引用方**: `lib.rs`

## 实现备注

- Azure 端点在 `store=true` 时自动为输入项附加 ID。
- 请求头包含 `Accept: text/event-stream`。
