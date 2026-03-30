# `protocol_v1.rs` — Realtime V1 事件解析

## 功能概述

实现 Realtime V1 协议的服务端事件解析，处理 `session.updated`、`conversation.output_audio.delta`、转录增量、`conversation.item.added/done`、`conversation.handoff.requested` 和错误事件。音频事件要求显式提供 sample_rate 和 channels。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_realtime_event_v1` | `fn(payload) -> Option<RealtimeEvent>` | V1 事件解析入口 |

## 依赖关系

- **被引用方**: `protocol.rs`
