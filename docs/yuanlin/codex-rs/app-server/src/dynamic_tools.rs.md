# `dynamic_tools.rs` — 动态工具调用响应处理

## 功能概述

本模块处理动态工具（Dynamic Tool）调用的客户端响应。当核心引擎通过 `ServerRequest` 向客户端发起动态工具调用后，客户端的响应通过 oneshot channel 返回。`on_call_response` 函数等待该响应，将其解码为 `DynamicToolCallResponse`，转换为核心层的 `CoreDynamicToolResponse`，然后通过 `Op::DynamicToolResponse` 提交回对话线程。对于失败或无效的响应，会生成包含错误描述的 fallback 响应。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| 无自定义结构体 | — | 仅包含函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `on_call_response` | `async fn(call_id, receiver, conversation)` | 等待客户端响应并提交动态工具结果到对话线程 |
| `decode_response` | `fn(value) -> (DynamicToolCallResponse, Option<String>)` | 尝试反序列化响应，失败时返回 fallback |
| `fallback_response` | `fn(message) -> (DynamicToolCallResponse, Option<String>)` | 生成 `success: false` 的错误 fallback 响应 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`（`DynamicToolCallResponse`）、`codex_core`（`CodexThread`、`Op`）、`codex_protocol::dynamic_tools`
- **被引用方**: `bespoke_event_handling.rs`（处理动态工具调用事件时调用）

## 实现备注

- 当响应是 turn transition 错误时（由 `is_turn_transition_server_request_error` 检测），直接返回而不提交 fallback。
