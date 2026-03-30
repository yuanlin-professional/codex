# `session.rs` — HTTP 请求会话管理

## 功能概述

`EndpointSession` 是内部请求会话层，封装 HTTP transport、provider 和 auth，提供 `execute`（单次请求）和 `stream_with`（流式请求）方法。支持通过回调自定义请求、认证头注入和带重试的请求遥测。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `EndpointSession<T, A>` | struct | HTTP 请求会话（transport + provider + auth + telemetry） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `execute_with` | `async fn(&self, method, path, headers, body, configure) -> Result<Response>` | 带自定义配置的单次请求 |
| `stream_with` | `async fn(&self, method, path, headers, body, configure) -> Result<StreamResponse>` | 带自定义配置的流式请求 |

## 依赖关系

- **被引用方**: `compact.rs`, `memories.rs`, `models.rs`, `responses.rs`
