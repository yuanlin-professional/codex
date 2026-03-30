# `retry.rs` — HTTP 请求重试逻辑

## 功能概述

实现 HTTP 请求的重试策略。根据错误类型（速率限制、服务器错误等）决定是否重试，并计算指数退避延迟。

## 依赖关系

- **被引用方**: `codex-client/src/transport.rs`
