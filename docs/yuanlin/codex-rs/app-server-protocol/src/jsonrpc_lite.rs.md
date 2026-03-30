# `jsonrpc_lite.rs` — 轻量级 JSON-RPC 消息类型

## 功能概述

定义了 Codex app-server 使用的轻量级 JSON-RPC 消息格式。与标准 JSON-RPC 2.0 不同的是，不发送也不期望 `"jsonrpc": "2.0"` 字段。包括请求 ID（字符串或整数）、四种消息类型（请求、通知、成功响应、错误响应）的完整类型定义。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RequestId` | 枚举 | 请求标识符（String 或 Integer） |
| `JSONRPCMessage` | 枚举 | JSON-RPC 消息联合类型 |
| `JSONRPCRequest` | 结构体 | 带 ID 和方法名的请求消息，支持可选 trace 上下文 |
| `JSONRPCNotification` | 结构体 | 无需响应的通知消息 |
| `JSONRPCResponse` | 结构体 | 成功响应 |
| `JSONRPCError` | 结构体 | 错误响应 |
| `JSONRPCErrorError` | 结构体 | 错误详情（错误码 + 消息 + 可选数据） |

## 依赖关系

- **引用的 crate**: `codex_protocol`, `schemars`, `serde`, `ts_rs`
- **被引用方**: `common.rs` 中的协议消息解析和构造

## 实现备注

- `RequestId` 使用 `#[serde(untagged)]` 支持无标签反序列化。
- `JSONRPCRequest` 支持可选的 W3C Trace Context，用于分布式追踪。
- `Result` 类型别名为 `serde_json::Value`，不做进一步类型化。
