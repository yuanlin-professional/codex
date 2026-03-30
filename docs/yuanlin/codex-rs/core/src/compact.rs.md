# `compact.rs` — 上下文压缩（Compaction）实现

## 功能概述

该文件实现了 Codex 的对话历史压缩功能，当模型上下文窗口接近满载时，通过调用 LLM 对历史记录进行摘要化，缩减 token 消耗。支持两种压缩模式：手动/回合前压缩（`DoNotInject`，压缩后清除初始上下文引用，让下次回合重新注入）和回合中自动压缩（`BeforeLastUserMessage`，将初始上下文注入到最后一条用户消息之前）。压缩流程包括构建摘要请求、处理模型流响应、构建压缩后历史（保留最近用户消息和摘要）、替换会话历史。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `InitialContextInjection` | enum | 初始上下文注入策略：`BeforeLastUserMessage`（回合中）/ `DoNotInject`（回合前） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_inline_auto_compact_task` | `async fn(sess, turn_context, injection) -> CodexResult<()>` | 执行自动内联压缩（回合中触发） |
| `run_compact_task` | `async fn(sess, turn_context, input) -> CodexResult<()>` | 执行手动/独立压缩任务 |
| `run_compact_task_inner` | `async fn(...) -> CodexResult<()>` | 压缩核心逻辑：流请求、重试、历史替换 |
| `build_compacted_history` | `fn(initial_context, user_messages, summary_text) -> Vec<ResponseItem>` | 构建压缩后的替换历史 |
| `insert_initial_context_before_last_real_user_or_summary` | `fn(history, initial_context) -> Vec<ResponseItem>` | 将初始上下文插入到压缩历史的正确位置 |
| `collect_user_messages` | `fn(items) -> Vec<String>` | 从历史中收集非摘要的用户消息 |
| `is_summary_message` | `fn(message: &str) -> bool` | 判断消息是否为压缩摘要 |
| `drain_to_completed` | `async fn(sess, turn_context, client_session, ..., prompt) -> CodexResult<()>` | 消费模型流直到收到 completed 事件 |
| `content_items_to_text` | `fn(content: &[ContentItem]) -> Option<String>` | 将内容项列表转为纯文本 |
| `should_use_remote_compact_task` | `fn(provider) -> bool` | 判断是否使用远端压缩任务 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（模型类型、历史项）、`codex_utils_output_truncation`（token 估算和截断）、`futures`
- **被引用方**: Session 主循环在 token 使用量过高时触发压缩

## 实现备注

- 压缩后的用户消息保留预算为 `COMPACT_USER_MESSAGE_MAX_TOKENS`（20000 tokens），从最近消息向前选取，超出部分被截断。
- 当压缩请求本身超出上下文窗口时，会从历史头部逐项移除并重试（`ContextWindowExceeded` 处理）。
- 重试采用指数退避策略（`backoff`），最大重试次数由模型提供商决定。
- `GhostSnapshot` 类型的历史项会在压缩后被保留到新历史中。
- 压缩完成后会发送警告消息，提醒用户长线程和多次压缩可能降低模型准确性。
- `SUMMARIZATION_PROMPT` 和 `SUMMARY_PREFIX` 通过 `include_str!` 从模板文件加载。
