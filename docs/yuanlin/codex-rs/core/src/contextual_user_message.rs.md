# `contextual_user_message.rs` — 上下文用户消息片段识别与解析

## 功能概述

此文件定义了 Codex 注入到用户消息中的各种上下文片段（如环境上下文、Shell 命令、回合中止通知、子代理通知等），并提供了识别和解析这些片段的工具函数。它还实现了 Hook Prompt 消息的解析逻辑，用于将包含 hook 片段的用户消息解析为 `HookPromptItem`。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ENVIRONMENT_CONTEXT_FRAGMENT` | 常量 | 环境上下文片段定义 |
| `USER_SHELL_COMMAND_FRAGMENT` | 常量 | 用户 Shell 命令片段定义 |
| `TURN_ABORTED_FRAGMENT` | 常量 | 回合中止通知片段定义 |
| `SUBAGENT_NOTIFICATION_FRAGMENT` | 常量 | 子代理通知片段定义 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `is_contextual_user_fragment` | `(content_item: &ContentItem) -> bool` | 判断内容项是否为上下文注入片段 |
| `is_memory_excluded_contextual_user_fragment` | `(content_item: &ContentItem) -> bool` | 判断内容项是否应从记忆输入中排除 |
| `parse_visible_hook_prompt_message` | `(id, content) -> Option<HookPromptItem>` | 从用户消息内容中解析 Hook Prompt |

## 依赖关系

- **引用的 crate**: `codex_instructions`（片段定义类型）、`codex_protocol`（Hook Prompt 类型）
- **被引用方**: `event_mapping.rs`（上下文消息过滤）、`environment_context.rs`、记忆系统

## 实现备注

- 上下文片段通过 XML 标签的开闭对识别（如 `<environment_context>...</environment_context>`）
- AGENTS.md 和技能片段被排除在记忆输入之外（它们是提示脚手架而非对话内容）
- 环境上下文和子代理通知保留在记忆输入中（包含有用的执行上下文）
- Hook Prompt 消息允许混合标准上下文片段和 hook 片段，但不允许非上下文的普通文本
