# `lib.rs` — codex-api crate 入口

## 功能概述

codex-api 库的根模块文件，声明所有公共子模块（auth、common、endpoint、error、provider、rate_limits、requests、sse、telemetry），并通过大量 `pub use` 语句统一导出核心公共类型供外部 crate 使用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| 各种 Client 类型 | re-export | CompactClient, MemoriesClient, ModelsClient, ResponsesClient, RealtimeWebsocketClient 等 |
| ResponseEvent / ResponseStream | re-export | 流式响应事件和流 |
| ApiError | re-export | 统一错误类型 |
| Provider | re-export | API 端点配置 |
| AuthProvider | re-export | 认证 trait |

## 依赖关系

- **引用的 crate**: `codex_client`, `codex_protocol`
- **被引用方**: 所有使用 `codex_api` crate 的外部代码
