# `interrupts.rs` — 交互式中断事件队列管理

## 功能概述

本模块管理 TUI 中需要中断当前流程的事件队列。`InterruptManager` 使用 `VecDeque` 按 FIFO 顺序排队各类中断事件（执行审批、补丁审批、MCP elicitation、权限请求、用户输入、命令开始/结束等），并在适当时机通过 `flush_all` 批量分发给 `ChatWidget` 处理。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `QueuedInterrupt` | enum | 排队中断类型：ExecApproval、ApplyPatchApproval、Elicitation、RequestPermissions、RequestUserInput、ExecBegin/End、McpBegin/End、PatchEnd |
| `InterruptManager` | struct | 中断事件队列管理器，内含 `VecDeque<QueuedInterrupt>` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `push_exec_approval` | `(&mut self, ev)` | 将执行审批事件加入队列 |
| `push_user_input` | `(&mut self, ev)` | 将用户输入请求事件加入队列 |
| `flush_all` | `(&mut self, chat)` | 按 FIFO 顺序将所有排队事件分发给 ChatWidget |
| `is_empty` | `(&self) -> bool` | 检查队列是否为空 |

## 依赖关系

- **引用的 crate**: `codex_protocol::protocol`, `codex_protocol::approvals`
- **被引用方**: `ChatWidget` 使用此管理器延迟处理中断事件

## 实现备注

- 所有事件通过对应的 `push_*` 方法入队，通过 `flush_all` 统一出队
- `flush_all` 将每种中断类型映射到 `ChatWidget` 上对应的 `handle_*_now` 方法
