# `transport.rs` — HTTP 传输层

## 功能概述

实现与 OpenAI API 通信的核心 HTTP 传输层。管理 SSE 连接的建立、请求发送、响应流处理和错误恢复。集成重试逻辑和遥测收集。

## 依赖关系

- **被引用方**: core 的 Responses API 调用
