# `protocol_v2.rs` — Realtime V2 事件解析

## 功能概述

实现 Realtime V2 协议的服务端事件解析。相比 V1，增加了 `response.output_audio.delta`/`response.audio.delta`（默认 24kHz 采样率）、`input_audio_buffer.speech_started`、`response.cancelled`、`response.done` 等事件类型。handoff 通过检测 `function_call` 类型中名为 "codex" 的工具调用来识别。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TOOL_ARGUMENT_KEYS` | const | 工具参数中提取 input_transcript 的备选键列表 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_realtime_event_v2` | `fn(payload) -> Option<RealtimeEvent>` | V2 事件解析入口 |
| `parse_handoff_requested_event` | `fn(item) -> Option<RealtimeEvent>` | 从 function_call item 提取 handoff |
| `extract_input_transcript` | `fn(arguments) -> String` | 从工具参数 JSON 提取用户输入 |

## 依赖关系

- **被引用方**: `protocol.rs`

## 实现备注

- 音频采样率和通道数有默认值（24kHz/1ch），与 V1 的必须字段不同。
- `response.done` 可能在 output 数组中包含 handoff 函数调用。
