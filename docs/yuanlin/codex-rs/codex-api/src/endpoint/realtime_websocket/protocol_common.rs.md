# `protocol_common.rs` — Realtime 事件解析通用函数

## 功能概述

提供 V1 和 V2 共用的 Realtime WebSocket 事件解析辅助函数：JSON payload 解析、session.updated 事件解析、转录增量解析和错误事件解析。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_realtime_payload` | `fn(payload, parser_name) -> Option<(Value, String)>` | 解析 JSON 并提取 type 字段 |
| `parse_session_updated_event` | `fn(parsed) -> Option<RealtimeEvent>` | 解析 session.updated 事件 |
| `parse_transcript_delta_event` | `fn(parsed, field) -> Option<TranscriptDelta>` | 解析转录增量 |
| `parse_error_event` | `fn(parsed) -> Option<RealtimeEvent>` | 解析错误事件 |

## 依赖关系

- **被引用方**: `protocol_v1.rs`, `protocol_v2.rs`
