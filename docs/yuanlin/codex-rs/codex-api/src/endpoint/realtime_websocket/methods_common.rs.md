# `methods_common.rs` — Realtime WebSocket 通用方法分发

## 功能概述

根据 `RealtimeEventParser`（V1 或 RealtimeV2）分发创建会话项、追加 handoff 消息、更新会话和获取 WebSocket intent 的调用到对应版本的实现。V1 强制使用 Conversational 模式。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `conversation_item_create_message` | `fn(parser, text) -> RealtimeOutboundMessage` | 根据版本构造会话项消息 |
| `conversation_handoff_append_message` | `fn(parser, handoff_id, text) -> RealtimeOutboundMessage` | 构造 handoff 追加消息（含前缀） |
| `session_update_session` | `fn(parser, instructions, mode) -> SessionUpdateSession` | 构造会话更新配置 |
| `websocket_intent` | `fn(parser) -> Option<&str>` | 返回 WebSocket intent 参数 |

## 依赖关系

- **被引用方**: `realtime_websocket/methods.rs`
