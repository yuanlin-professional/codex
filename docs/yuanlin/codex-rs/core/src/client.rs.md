# `client.rs` — 模型 API 客户端：会话级与 Turn 级的请求传输层

## 功能概述

`client.rs`（约 1823 行）实现了与模型提供商 API 通信的核心客户端层。`ModelClient` 是会话级别的客户端，持有认证、provider 选择、会话 ID 和 WebSocket 回退状态等稳定配置。`ModelClientSession` 是 Turn 级别的流式会话，懒加载 WebSocket 连接并在同一 Turn 内复用，支持增量请求和粘性路由（sticky routing）。文件还实现了 WebSocket 预热（prewarm）、HTTP SSE 回退、401 认证恢复、请求压缩、遥测上报等功能。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ModelClient` | struct | 会话级客户端，持有 `Arc<ModelClientState>`，支持 clone 共享 |
| `ModelClientState` | struct | 内部状态：auth_manager、conversation_id、provider、session_source、WebSocket 禁用标志、缓存的 WebSocket 会话等 |
| `ModelClientSession` | struct | Turn 级流式会话，持有 WebSocket 连接、上一次请求缓存、粘性路由 token（`OnceLock<String>`） |
| `WebsocketSession` | struct | WebSocket 会话内部状态：连接、上次请求、上次响应接收器、连接复用标志 |
| `WebsocketStreamOutcome` | enum | WebSocket 流结果：`Stream` 或 `FallbackToHttp` |
| `CurrentClientSetup` | struct | 当前请求的 auth/provider 解析结果 |
| `AuthRequestTelemetryContext` | struct | 认证请求遥测上下文 |
| `PendingUnauthorizedRetry` | struct | 待处理的 401 重试状态 |
| `ApiTelemetry` | struct | API 遥测实现，同时实现 `RequestTelemetry`、`SseTelemetry`、`WebsocketTelemetry` trait |
| `LastResponse` | struct | 上次响应的 response_id 和 items_added，用于增量请求 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ModelClient::new` | `fn new(auth_manager, conversation_id, provider, ...)` | 创建会话级客户端 |
| `ModelClient::new_session` | `fn new_session(&self) -> ModelClientSession` | 创建 Turn 级流式会话 |
| `ModelClient::compact_conversation_history` | `async fn compact_conversation_history(...)` | 调用 Compact 端点压缩对话历史 |
| `ModelClient::summarize_memories` | `async fn summarize_memories(...)` | 调用 memories/trace_summarize 端点 |
| `ModelClient::responses_websocket_enabled` | `fn responses_websocket_enabled(&self) -> bool` | 判断 WebSocket 传输是否启用 |
| `ModelClient::force_http_fallback` | `fn force_http_fallback(...)` | 永久禁用 WebSocket 并回退到 HTTP |
| `ModelClient::connect_websocket` | `async fn connect_websocket(...)` | 打开 WebSocket 连接 |
| `ModelClientSession::stream` | `async fn stream(...)` | Turn 级流式请求，优先 WebSocket 失败回退 HTTP |
| `ModelClientSession::prewarm_websocket` | `async fn prewarm_websocket(...)` | WebSocket 预热（v2 warmup） |
| `ModelClientSession::preconnect_websocket` | `async fn preconnect_websocket(...)` | 预连接 WebSocket |
| `ModelClientSession::stream_responses_api` | `async fn stream_responses_api(...)` | HTTP SSE 流式请求 |
| `ModelClientSession::stream_responses_websocket` | `async fn stream_responses_websocket(...)` | WebSocket 流式请求 |
| `ModelClientSession::get_incremental_items` | `fn get_incremental_items(...)` | 计算增量输入项（用于 WebSocket 复用） |
| `handle_unauthorized` | `async fn handle_unauthorized(...)` | 处理 401：刷新 ChatGPT token 或返回错误 |
| `map_response_stream` | `fn map_response_stream(...)` | 将 API 流映射为内部 ResponseStream |

## 依赖关系

- **引用的 crate**: `codex_api`（API 传输层）、`codex_otel`（遥测）、`codex_protocol`（协议类型）、`codex_tools`（工具 JSON 生成）、`tokio`、`futures`、`http`、`reqwest`
- **被引用方**: `codex.rs`（Session 持有 `ModelClient`，`run_turn` 中使用 `ModelClientSession` 进行流式请求）

## 实现备注

1. **WebSocket v2 预热**：`prewarm_websocket` 发送 `generate=false` 的请求，等待完成后复用同一连接。
2. **增量请求**：当新请求是上一请求的严格扩展时，仅发送增量项 + `previous_response_id`，减少数据传输。
3. **粘性路由**：通过 `x-codex-turn-state` 头实现 Turn 内的服务端路由粘性。
4. **认证恢复**：收到 401 时尝试刷新 ChatGPT token（最多一次），然后重试请求。
5. **回退策略**：WebSocket 连接失败（包括 426 Upgrade Required）后永久回退到 HTTP SSE。
6. **Drop 回收**：`ModelClientSession` drop 时将 WebSocket 会话缓存回 `ModelClient`，供下一个 Turn 复用连接。
