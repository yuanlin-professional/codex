# `user_shell_command.rs` — 用户 Shell 命令记录格式化

## 功能概述

此文件负责将用户在 Codex 会话中执行的 shell 命令及其输出格式化为结构化的 XML 记录，然后包装为 `ResponseItem` 注入到对话历史中。格式化包含命令文本、退出码、执行时长和截断后的输出内容。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| （无独立类型定义） | — | — |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `format_user_shell_command_record` | `(command, exec_output, turn_context) -> String` | 格式化 shell 命令记录为带 XML 标签的字符串 |
| `user_shell_command_record_item` | `(command, exec_output, turn_context) -> ResponseItem` | 将命令记录转换为 ResponseItem 消息 |

## 依赖关系

- **引用的 crate**: `crate::contextual_user_message`（`USER_SHELL_COMMAND_FRAGMENT`）、`crate::exec`（`ExecToolCallOutput`）、`crate::tools`
- **被引用方**: 用户 shell 命令执行后记录到对话历史

## 实现备注

- 输出结构为 `<user_shell_command><command>...</command><result>...</result></user_shell_command>`
- 时长精确到 4 位小数秒
- 输出截断策略来自 `turn_context.truncation_policy`
- 使用 `USER_SHELL_COMMAND_FRAGMENT.wrap()` 和 `.into_message()` 实现 XML 包装和消息转换
