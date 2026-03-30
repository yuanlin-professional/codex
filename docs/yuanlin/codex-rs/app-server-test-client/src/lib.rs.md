# `lib.rs` — app-server 测试客户端

## 功能概述

功能完备的 Codex app-server 测试客户端，支持 stdio 和 WebSocket 两种传输方式。提供丰富的 CLI 子命令：发送消息（V1/V2）、恢复线程、触发命令/补丁审批、多命令审批、登录测试、速率限制查询、模型列表、线程列表、elicitation 暂停测试等。实现了完整的 JSON-RPC 请求/响应/通知处理循环，包括命令审批自动接受/拒绝策略。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Cli` / `CliCommand` | struct/enum | CLI 参数定义，20+ 个子命令 |
| `CodexClient` | struct | 核心客户端，持有传输层和审批状态 |
| `ClientTransport` | enum | Stdio 或 WebSocket 传输 |
| `CommandApprovalBehavior` | enum | 审批策略：AlwaysAccept 或 AbortOn(n) |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`、`codex_core`、`codex_otel`、`clap`、`tungstenite`
- **被引用方**: 手动测试和 E2E 测试工具
