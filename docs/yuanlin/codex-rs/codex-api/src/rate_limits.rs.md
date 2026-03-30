# `rate_limits.rs` — 速率限制解析

## 功能概述

从 HTTP 响应头和 WebSocket 事件中解析 Codex 速率限制信息。支持多个 limit family（codex、codex_secondary 等）的主次窗口（primary/secondary）解析、信用额度（credits）解析和促销消息提取。也处理 `codex.rate_limits` WebSocket 事件。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RateLimitError` | struct | 速率限制错误 |
| `RateLimitEvent` | struct | WebSocket 速率限制事件的反序列化结构 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_default_rate_limit` | `fn(headers) -> Option<RateLimitSnapshot>` | 解析默认 codex 限制头 |
| `parse_all_rate_limits` | `fn(headers) -> Vec<RateLimitSnapshot>` | 解析所有 limit family |
| `parse_rate_limit_event` | `fn(payload) -> Option<RateLimitSnapshot>` | 解析 WebSocket 限制事件 |
| `parse_promo_message` | `fn(headers) -> Option<String>` | 提取促销消息 |

## 依赖关系

- **引用的 crate**: `codex_protocol::protocol`, `codex_protocol::account`
- **被引用方**: `sse/responses.rs`, `endpoint/responses_websocket.rs`

## 实现备注

- header 名称使用 `x-{normalized_limit}-{tier}-{field}` 模式。
- limit_id 规范化：转小写，连字符替换为下划线。
