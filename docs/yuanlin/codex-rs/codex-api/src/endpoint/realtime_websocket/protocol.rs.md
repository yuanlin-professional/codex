# `protocol.rs` — Realtime WebSocket 协议类型定义

## 功能概述

定义 Realtime WebSocket 的所有协议类型：出站消息枚举（`RealtimeOutboundMessage`）、会话更新结构（`SessionUpdateSession`）、音频配置、会话类型、对话项类型、工具定义等。还包含解析器分发函数 `parse_realtime_event`，根据 `RealtimeEventParser` 版本路由到 V1 或 V2 解析器。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RealtimeEventParser` | enum | 解析器版本：V1 / RealtimeV2 |
| `RealtimeSessionMode` | enum | 会话模式：Conversational / Transcription |
| `RealtimeSessionConfig` | struct | 会话配置（instructions/model/session_id/parser/mode） |
| `RealtimeOutboundMessage` | enum | 出站消息类型（音频/会话项/session.update/response.create） |
| `SessionUpdateSession` | struct | session.update 的 session 字段 |
| `SessionType` | enum | 会话类型：Quicksilver/Realtime/Transcription |
| `AudioFormatType` | enum | 音频格式：audio/pcm |
| `SessionAudioVoice` | enum | 语音类型：Fathom/Marin |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_realtime_event` | `fn(payload, parser) -> Option<RealtimeEvent>` | 解析器版本分发 |

## 依赖关系

- **被引用方**: `methods.rs`, `methods_common.rs`
