# `bespoke_event_handling.rs` — 核心事件到 app-server 协议通知的转换层

## 功能概述

本模块是 app-server 中最大的事件处理模块（约 3900 行），负责将 `codex_core` 运行时产生的底层 `EventMsg` 事件流转换为 `codex_app_server_protocol` 定义的服务端通知和服务端请求。它实现了所有面向客户端的审批流程（命令执行审批、文件变更审批、权限请求审批、MCP elicitation）、Turn 生命周期管理（开始/完成/中断）、流式内容增量推送（agent message delta、reasoning delta、command output delta 等）、以及各种辅助通知（token 用量更新、diff 更新、context compaction、deprecation notice 等）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ApiVersion` | 枚举（间接使用） | 区分 v1/v2 API 版本以决定中断响应格式 |
| `TurnSummary` | 结构体（来自 `thread_state`） | 在 Turn 运行期间累积文件变更和命令执行的状态摘要 |
| `ThreadWatchActiveGuard` | RAII guard | 跟踪待处理的权限/用户输入请求数量 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `apply_bespoke_event_handling` | `async fn(event, thread_state, outgoing, ...)` | 核心入口：将单个 `EventMsg` 事件路由到对应的通知/请求处理逻辑 |
| 审批相关处理 | 多个内部函数 | 处理 `ExecCommandApproval`、`FileChangeApproval`、`ApplyPatchApproval`、`PermissionsApproval` 等审批请求 |
| Turn 生命周期处理 | 多个内部函数 | 处理 `TurnStarted`、`TurnCompleted`、`TurnInterrupted`、`TurnRollback` 等事件 |
| 流式内容推送 | 多个内部函数 | 处理 `AgentMessageDelta`、`ReasoningTextDelta`、`CommandExecutionOutputDelta` 等增量通知 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`（大量通知/请求类型）、`codex_core`（`EventMsg`、审批相关类型）、`codex_protocol`
- **被引用方**: `codex_message_processor` 模块中的线程监听器循环

## 实现备注

- 审批请求通过 `OutgoingMessageSender::send_request` 发送到客户端，客户端回复后通过 oneshot channel 返回结果，整个流程是异步等待的。
- Turn 完成时会清理 `TurnSummary` 状态并发送 `TurnCompleted` 通知。
- 对于 `ThreadWatchActiveGuard`，当 guard 被 drop 时会自动递减待处理请求计数。
