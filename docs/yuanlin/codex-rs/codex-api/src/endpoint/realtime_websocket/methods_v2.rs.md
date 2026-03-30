# `methods_v2.rs` — Realtime V2 协议方法实现

## 功能概述

实现 Realtime V2 协议的消息构造，支持 Conversational 和 Transcription 两种模式。Conversational 模式包含 codex 工具函数定义、VAD 转向检测、噪声抑制和 marin 语音。Transcription 模式仅配置音频输入。handoff 响应使用 `function_call_output` 类型。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `conversation_item_create_message` | `fn(text) -> RealtimeOutboundMessage` | V2 消息（content type = InputText） |
| `conversation_handoff_append_message` | `fn(handoff_id, text) -> RealtimeOutboundMessage` | V2 使用 FunctionCallOutput 类型 |
| `session_update_session` | `fn(instructions, mode) -> SessionUpdateSession` | 根据模式配置不同的会话参数 |
| `websocket_intent` | `fn() -> Option<&str>` | 返回 None（V2 不需要 intent） |

## 依赖关系

- **被引用方**: `methods_common.rs`

## 实现备注

- Conversational 模式注册名为 "codex" 的 function tool，描述为委托请求。
- Transcription 模式省略 instructions、output、tools 和 tool_choice。
