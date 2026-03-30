# `lib.rs` — Responses API 代理

## 功能概述

提供将 Responses API 请求代理到 OpenAI 端点的 HTTP 服务器。接收本地请求，注入认证信息后转发到配置的 base URL，并将 SSE 响应流式返回给客户端。

## 依赖关系

- **被引用方**: `responses-api-proxy/src/main.rs`
