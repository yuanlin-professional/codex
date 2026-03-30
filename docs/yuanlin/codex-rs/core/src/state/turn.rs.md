# `turn.rs` — 轮次级状态与活跃任务管理

## 功能概述

`turn.rs` 定义了单个对话轮次（turn）的状态管理结构。`ActiveTurn` 负责跟踪当前轮次中所有正在运行的任务（`RunningTask`），而 `TurnState` 则管理轮次内的各类挂起审批（pending approvals）、用户输入请求、MCP elicitation、动态工具请求以及缓冲输入等可变状态。这些结构协同工作，确保在一个轮次的生命周期内正确管理异步操作和用户交互流程。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ActiveTurn` | struct | 当前活跃轮次的元数据容器，持有任务列表和轮次状态 |
| `RunningTask` | struct | 单个运行中任务的完整信息，包括完成通知、取消令牌、任务句柄等 |
| `TaskKind` | enum | 任务类型枚举：`Regular`（常规）、`Review`（审查）、`Compact`（压缩） |
| `TurnState` | struct | 单轮次的可变状态，管理各类挂起的异步操作和缓冲输入 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ActiveTurn::add_task` | `fn add_task(&mut self, task: RunningTask)` | 向当前轮次添加一个运行中的任务 |
| `ActiveTurn::remove_task` | `fn remove_task(&mut self, sub_id: &str) -> bool` | 移除指定任务，返回是否所有任务已完成 |
| `ActiveTurn::drain_tasks` | `fn drain_tasks(&mut self) -> Vec<RunningTask>` | 取出并清空所有任务 |
| `ActiveTurn::clear_pending` | `async fn clear_pending(&self)` | 清除当前轮次所有挂起的审批和缓冲输入 |
| `TurnState::insert_pending_approval` | `fn insert_pending_approval(&mut self, key, tx)` | 插入一个挂起的工具调用审批 |
| `TurnState::remove_pending_approval` | `fn remove_pending_approval(&mut self, key) -> Option<...>` | 移除并返回指定的挂起审批 |
| `TurnState::insert_pending_elicitation` | `fn insert_pending_elicitation(&mut self, server_name, request_id, tx)` | 插入 MCP elicitation 请求 |
| `TurnState::push_pending_input` | `fn push_pending_input(&mut self, input: ResponseInputItem)` | 向缓冲区追加待处理输入 |
| `TurnState::take_pending_input` | `fn take_pending_input(&mut self) -> Vec<ResponseInputItem>` | 取出并清空所有缓冲输入 |
| `TurnState::record_granted_permissions` | `fn record_granted_permissions(&mut self, permissions)` | 记录轮次内授予的权限 |

## 依赖关系

- **引用的 crate**: `codex_protocol`, `codex_sandboxing`, `codex_rmcp_client`, `rmcp`, `indexmap`, `tokio`, `tokio_util`
- **引用的内部模块**: `TurnContext`, `ReviewDecision`, `TokenUsage`, `SessionTask`
- **被引用方**: `Session` 通过 `ActiveTurn` 管理当前轮次的所有任务和状态

## 实现备注

- 使用 `IndexMap` 存储任务，确保按插入顺序遍历
- `TurnState` 通过 `Arc<Mutex<TurnState>>` 在 `ActiveTurn` 中共享，支持跨异步任务的安全访问
- `RunningTask` 中的 `_timer` 字段用于在任务 drop 时记录完整轮次持续时间（telemetry 用途）
- `pending_elicitations` 使用 `(String, RequestId)` 复合键来唯一标识 MCP 服务器的 elicitation 请求
- `prepend_pending_input` 通过交换向量实现高效的前置插入
