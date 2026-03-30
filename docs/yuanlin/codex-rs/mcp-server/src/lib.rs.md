# `lib.rs` — MCP Server 核心库

## 功能概述

Codex MCP（Model Context Protocol）服务器的核心库。实现 MCP 协议的服务端，提供 Codex 工具调用能力给外部 MCP 客户端。处理消息路由、工具注册、执行审批和补丁审批。

## 依赖关系

- **被引用方**: `mcp-server/src/main.rs`
