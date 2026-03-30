# `methods_v1.rs` — Realtime V1 协议方法实现

## 功能概述

实现 Realtime V1（Quicksilver）协议的具体消息构造：会话项创建、handoff 追加、会话更新配置（PCM 24kHz 输入、fathom 语音输出）。WebSocket intent 为 "quicksilver"。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `conversation_item_create_message` | `fn(text) -> RealtimeOutboundMessage` | V1 文本消息（content type = Text） |
| `session_update_session` | `fn(instructions) -> SessionUpdateSession` | V1 会话配置（quicksilver 类型） |
| `websocket_intent` | `fn() -> Option<&str>` | 返回 "quicksilver" |

## 依赖关系

- **被引用方**: `methods_common.rs`
