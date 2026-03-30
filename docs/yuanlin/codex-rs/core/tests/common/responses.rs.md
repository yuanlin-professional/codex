# `responses.rs` — Responses API 模拟与请求捕获工具

## 功能概述

为 Codex Core 集成测试提供完整的 OpenAI Responses API 模拟基础设施。包括：(1) 基于 wiremock 的 SSE 和 JSON 响应挂载；(2) 请求捕获与检查（`ResponseMock`、`ResponsesRequest`）；(3) WebSocket 测试服务器；(4) 丰富的 SSE 事件构建辅助函数；(5) 请求体不变式验证（确保 function_call_output 与 function_call 配对）。该模块是几乎所有 Core 集成测试的底层依赖。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ResponseMock` | struct | 请求记录器，实现 `wiremock::Match` trait，捕获所有匹配的请求并存储为 `ResponsesRequest` |
| `ResponsesRequest` | struct | 对 `wiremock::Request` 的包装，提供 `body_json()`、`input()`、`header()`、`message_input_texts()` 等丰富的请求检查方法 |
| `WebSocketTestServer` | struct | 轻量级 WebSocket 测试服务器，支持多连接、多请求序列、自定义响应头和延迟握手 |
| `WebSocketConnectionConfig` | struct | WebSocket 连接配置，包括请求序列、响应头、握手延迟和关闭策略 |
| `ModelsMock` | struct | `/models` 端点请求记录器 |
| `FunctionCallResponseMocks` | struct | function_call 双阶段响应的 mock 集合 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_mock_server` | `async fn() -> MockServer` | 启动 wiremock 服务器并预挂载空 `/models` 响应 |
| `sse` | `fn(events: Vec<Value>) -> String` | 将 JSON 事件数组转换为 SSE 文本流 |
| `mount_sse_once` | `async fn(server, body) -> ResponseMock` | 挂载单次 SSE 响应并返回请求记录器 |
| `mount_sse_sequence` | `async fn(server, bodies) -> ResponseMock` | 按 FIFO 顺序挂载多个 SSE 响应 |
| `mount_response_sequence` | `async fn(server, responses) -> ResponseMock` | 按 FIFO 顺序挂载多个 ResponseTemplate |
| `mount_compact_user_history_with_summary_once` | `async fn(server, summary_text) -> ResponseMock` | 挂载模拟远程 compaction 行为的 `/responses/compact` 端点 |
| `start_websocket_server` | `async fn(connections) -> WebSocketTestServer` | 启动 WebSocket 测试服务器 |
| `ev_response_created` / `ev_completed` / `ev_assistant_message` / `ev_function_call` / `ev_shell_command_call` / `ev_apply_patch_call` 等 | 各种 `fn(...) -> Value` | 构建特定类型的 SSE 事件 JSON |
| `validate_request_body_invariants` | `fn(request: &wiremock::Request)` | 验证请求体中 function_call/output 配对完整性 |

## 依赖关系

- **引用的 crate**: `wiremock`、`serde_json`、`tokio`、`tokio_tungstenite`、`base64`、`futures`、`zstd`
- **被引用方**: 被所有 Core 集成测试通过 `core_test_support::responses` 引用

## 实现备注

- `ResponseMock` 同时实现 `Match` trait，在匹配时自动记录请求并执行 `validate_request_body_invariants` 检查
- `ResponsesRequest::body_json()` 支持自动解压 zstd 编码的请求体
- WebSocket 服务器支持 per-message deflate 压缩扩展
- `validate_request_body_invariants` 确保每个 call 类型都有对应的 output、每个 output 都有对应的 call，防止孤立的 call/output 回归
