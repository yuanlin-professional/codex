# `compact_remote.rs` — 远程上下文压缩（Remote Compaction）

## 功能概述

此文件实现了 Codex 的远程上下文压缩机制，用于在对话历史过长时通过模型 API 对历史记录进行摘要压缩。它支持两种压缩场景：回合中自动压缩（inline auto-compact）和独立压缩任务。压缩过程会保留关键历史项（如用户消息、Ghost 快照），过滤掉开发者指令和上下文注入消息，并在压缩后重新注入初始上下文。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CompactRequestLogData` | 结构体（私有） | 记录压缩请求失败时的日志数据，包含请求的可见字节数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_inline_remote_auto_compact_task` | `async (sess, turn_context, initial_context_injection) -> CodexResult<()>` | 回合中自动触发的远程压缩入口 |
| `run_remote_compact_task` | `async (sess, turn_context) -> CodexResult<()>` | 独立远程压缩任务入口，会发送 TurnStarted 事件 |
| `process_compacted_history` | `async (sess, turn_context, compacted_history, injection) -> Vec<ResponseItem>` | 处理压缩后的历史记录，过滤无效项并注入初始上下文 |
| `should_keep_compacted_history_item` | `(item: &ResponseItem) -> bool` | 判断压缩输出中的项是否应保留 |
| `trim_function_call_history_to_fit_context_window` | `(history, turn_context, base_instructions) -> usize` | 压缩前先从末尾修剪函数调用历史以适配上下文窗口 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`ResponseItem`、`TurnItem`）、`crate::codex::Session`、`crate::context_manager`、`crate::compact`、`crate::event_mapping`
- **被引用方**: 会话（Session）的压缩流程调用此模块

## 实现备注

- 压缩输出中 `developer` 角色的消息会被丢弃，因为远程输出可能包含过时/重复的指令内容
- `user` 角色消息仅保留真实用户消息和持久化的 Hook Prompt，过滤上下文注入消息
- Ghost 快照（GhostSnapshot）在压缩后会被重新附加到历史中，以保证 `/undo` 功能可用
- 回合中压缩将初始上下文注入到最后一条用户消息之前，而回合前压缩则在压缩项之后注入
- 压缩失败时会记录详细的令牌使用和字节数统计信息用于诊断
