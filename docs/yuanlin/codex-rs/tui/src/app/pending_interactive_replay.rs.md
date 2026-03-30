# `pending_interactive_replay.rs` — 交互式提示的快照回放过滤

## 功能概述

本模块跟踪线程事件缓冲区中哪些交互式提示（审批、用户输入、MCP elicitation）仍未解决。当切换线程/代理时进行快照回放，只有仍处于 pending 状态的交互式提示才会被重放。状态通过入站事件（`note_event`）、出站操作（`note_outbound_op`）和缓冲区淘汰（`note_evicted_event`）进行更新。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PendingInteractiveReplayState` | struct | 跟踪各类未解决交互式请求的 HashSet 和 HashMap 集合 |
| `PendingInteractiveRequest` | enum | 待处理请求类型：ExecApproval、PatchApproval、Elicitation、RequestPermissions、RequestUserInput |
| `ElicitationRequestKey` | struct | MCP elicitation 的复合键 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `note_server_request` | `(&mut self, request)` | 记录新的服务器请求到待处理集合 |
| `note_outbound_op` | `(&mut self, op)` | 当用户提交操作时移除对应的待处理状态 |
| `note_server_notification` | `(&mut self, notification)` | 处理通知事件（如 TurnCompleted 清除关联请求） |
| `should_replay_snapshot_request` | `(&self, request) -> bool` | 判断快照回放时是否应包含此请求 |
| `has_pending_thread_approvals` | `(&self) -> bool` | 检查是否有待处理的线程审批 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`, `codex_protocol`
- **被引用方**: `ThreadEventStore` 使用此状态过滤快照回放

## 实现备注

- `request_user_input` 使用 FIFO 队列，因为同一 turn_id 可能有多个排队的提示
- 按 turn_id 索引的映射确保 `TurnComplete`/`TurnAborted` 可以清除关联的过时提示
- `request_user_input` 不计入 `has_pending_thread_approvals`（它是输入而非审批）
- 包含 15+ 个测试用例覆盖各种解决和清除场景
