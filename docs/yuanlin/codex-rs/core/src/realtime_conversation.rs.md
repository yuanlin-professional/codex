# `realtime_conversation.rs` — 实时对话管理器

## 功能概述

`realtime_conversation.rs`（约 1050 行）实现了 Codex 的实时（Realtime）对话功能，支持通过 WebSocket 连接到 OpenAI Realtime API 进行双向音频/文本对话。`RealtimeConversationManager` 管理对话状态（启动、音频输入、文本输入、handoff 输出、关闭），并通过异步任务处理来自 Realtime API 的事件流。支持 V1 和 V2 两种 Realtime 会话协议，包括 response 管理、音频截断、handoff 机制（将 Realtime 对话转交给 Codex 文本模式处理）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RealtimeConversationManager` | struct | 对话管理器，持有 `Mutex<Option<ConversationState>>` |
| `ConversationState` | struct | 活跃对话状态：audio_tx、user_text_tx、writer、handoff、input_task、fanout_task、realtime_active 标志 |
| `RealtimeHandoffState` | struct | Handoff 状态管理：output_tx、active_handoff、last_output_text、session_kind |
| `HandoffOutput` | enum | Handoff 输出：ImmediateAppend（V1）/ FinalToolCall（V2） |
| `RealtimeInputTask` | struct | 输入任务参数集 |
| `RealtimeSessionKind` | enum | V1 / V2 两种会话协议 |
| `RealtimeConversationEnd` | enum | 对话结束原因：Requested / TransportClosed / Error |
| `OutputAudioState` | struct | 输出音频状态追踪（item_id + audio_end_ms） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `RealtimeConversationManager::start` | `async fn start(api_provider, headers, session_config)` | 启动实时对话，建立 WebSocket 连接 |
| `RealtimeConversationManager::audio_in` | `async fn audio_in(frame)` | 发送音频帧输入 |
| `RealtimeConversationManager::text_in` | `async fn text_in(text)` | 发送文本输入 |
| `RealtimeConversationManager::handoff_out` | `async fn handoff_out(output_text)` | 输出 handoff 文本 |
| `RealtimeConversationManager::handoff_complete` | `async fn handoff_complete()` | 完成 handoff（V2 发送 FinalToolCall） |
| `RealtimeConversationManager::shutdown` | `async fn shutdown()` | 关闭对话 |
| `handle_start` | `async fn handle_start(sess, sub_id, params)` | 处理对话启动请求 |
| `handle_audio` | `async fn handle_audio(sess, sub_id, params)` | 处理音频输入 |
| `handle_text` | `async fn handle_text(sess, sub_id, params)` | 处理文本输入 |
| `handle_close` | `async fn handle_close(sess, sub_id)` | 处理对话关闭 |
| `spawn_realtime_input_task` | `fn spawn_realtime_input_task(input) -> JoinHandle<()>` | 启动输入处理任务（tokio::select! 多路复用） |
| `prepare_realtime_start` | `async fn prepare_realtime_start(sess, params)` | 准备实时对话启动参数（API key、指令、startup context） |
| `realtime_text_from_handoff_request` | `fn ...` | 从 handoff 请求中提取文本 |

## 依赖关系

- **引用的 crate**: `codex_api`（Realtime WebSocket 客户端和事件）、`codex_protocol`（协议类型）、`base64`、`tokio`、`async_channel`
- **被引用方**: `codex.rs`（Session 持有 `Arc<RealtimeConversationManager>`，`submission_loop` 处理 Realtime 操作）

## 实现备注

1. **V1 vs V2 协议**：V1 使用 ImmediateAppend handoff 输出，V2 使用 FinalToolCall + response.create 流程。
2. **Response 管理**（V2）：跟踪 `response_in_progress` 和 `pending_response_create`，在 response.done 后自动发送排队的 response.create。
3. **音频截断**（V2）：当检测到用户开始说话（InputAudioSpeechStarted）时，发送 `conversation.item.truncate` 截断当前输出音频。
4. **Handoff 机制**：Realtime 模型通过 HandoffRequested 事件将对话转交给 Codex 文本模式处理（`route_realtime_text_input`）。
5. **Fanout 任务**：独立的 tokio task 负责将 Realtime 事件分发到 Session 事件流，并处理 handoff 路由。
6. **Startup Context**：从当前 Session 状态构建 Realtime 启动上下文（token 预算 5000），附加到 instructions 中。
