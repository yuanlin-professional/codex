# `codex_thread.rs` — Codex 会话线程管理

## 功能概述

此文件定义了 `CodexThread`，它是 Codex 中一个完整对话线程（以前称为"会话"）的双向消息流管道。`CodexThread` 封装了底层的 `Codex` 实例，提供了提交操作、接收事件、管理线程状态、注入消息和控制带外引出（out-of-band elicitation）等功能。它还维护了配置快照和回滚路径。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ThreadConfigSnapshot` | 结构体 | 线程配置快照，包含模型、提供商、审批策略、沙箱策略等 |
| `CodexThread` | 结构体 | 核心线程管理类，封装 Codex 实例及相关状态 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `submit` | `async (&self, op: Op) -> CodexResult<String>` | 向线程提交一个操作 |
| `next_event` | `async (&self) -> CodexResult<Event>` | 从线程接收下一个事件 |
| `steer_input` | `async (&self, input, expected_turn_id) -> Result<String, SteerInputError>` | 在当前回合中注入引导输入 |
| `inject_user_message_without_turn` | `async (&self, message: String)` | 注入用户消息而不创建新的用户回合边界 |
| `increment_out_of_band_elicitation_count` | `async (&self) -> CodexResult<u64>` | 增加带外引出计数器，首次调用时暂停会话 |
| `decrement_out_of_band_elicitation_count` | `async (&self) -> CodexResult<u64>` | 减少带外引出计数器，归零时恢复会话 |
| `config_snapshot` | `async (&self) -> ThreadConfigSnapshot` | 获取当前线程配置快照 |

## 依赖关系

- **引用的 crate**: `crate::codex::Codex`、`crate::agent::AgentStatus`、`codex_protocol`（多种协议类型）、`codex_features`、`tokio`
- **被引用方**: `lib.rs` 公开导出 `CodexThread` 和 `ThreadConfigSnapshot`，供上层 TUI/app-server 使用

## 实现备注

- 带外引出（out-of-band elicitation）机制用于在回合进行中暂停/恢复会话，以支持外部交互
- `inject_user_message_without_turn` 在活跃回合存在时作为 pending input 注入，否则通过录制方式添加
- `append_message`（仅测试）支持在没有活跃回合时启动新回合以消费待处理输入
- `pending_message_input_item` 辅助函数仅支持 `ResponseItem::Message` 类型的转换
