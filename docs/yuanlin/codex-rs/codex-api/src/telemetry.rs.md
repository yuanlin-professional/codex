# `telemetry.rs` — 遥测 trait 和请求重试包装

## 功能概述

定义 `SseTelemetry`（SSE 轮询遥测）和 `WebsocketTelemetry`（WebSocket 请求/事件遥测）trait，以及 `run_with_request_telemetry` 包装函数。后者将 `codex_client::run_with_retry` 与每次请求的遥测回调结合，记录请求尝试的状态码、错误和耗时。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SseTelemetry` | trait | SSE 轮询遥测回调 |
| `WebsocketTelemetry` | trait | WebSocket 请求/事件遥测回调 |
| `WithStatus` | trait | 统一 Response/StreamResponse 的状态码提取 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_with_request_telemetry` | `async fn<T>(policy, telemetry, make_request, send) -> Result<T>` | 带遥测的重试请求执行 |

## 依赖关系

- **引用的 crate**: `codex_client`
- **被引用方**: `endpoint/session.rs`, `endpoint/responses_websocket.rs`
