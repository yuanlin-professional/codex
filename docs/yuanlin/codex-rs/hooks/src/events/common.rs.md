# `common.rs` -- 事件处理的公共工具函数与 matcher 逻辑

## 功能概述

本文件为 `events` 模块下所有事件处理器提供共享的工具函数，涵盖文本拼接、额外上下文追加、序列化失败时的 Hook 事件生成，以及 matcher 模式的验证和匹配逻辑。matcher 系统允许 Hook 通过正则表达式筛选特定的工具名或事件源。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| （无导出结构体） | -- | 本文件仅导出函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `join_text_chunks` | `fn(Vec<String>) -> Option<String>` | 将多个文本块用双换行拼接，空时返回 None |
| `trimmed_non_empty` | `fn(&str) -> Option<String>` | 去除首尾空白后返回非空字符串 |
| `append_additional_context` | `fn(&mut entries, &mut contexts, context)` | 同时将上下文追加到 entries 和 contexts 列表 |
| `flatten_additional_contexts` | `fn(iter) -> Vec<String>` | 将多个 handler 的上下文列表展平为单一列表 |
| `serialization_failure_hook_events` | `fn(handlers, turn_id, error) -> Vec<HookCompletedEvent>` | 为所有 handler 生成序列化失败的事件 |
| `matcher_pattern_for_event` | `fn(event_name, matcher) -> Option<&str>` | 根据事件类型决定是否保留 matcher 模式（UserPromptSubmit/Stop 忽略 matcher） |
| `validate_matcher_pattern` | `fn(&str) -> Result<(), regex::Error>` | 验证 matcher 模式是否为合法正则（`*` 和空串视为全匹配） |
| `matches_matcher` | `fn(matcher, input) -> bool` | 用正则匹配判断输入是否符合 matcher 模式 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（HookCompletedEvent 等）、`regex`
- **引用的内部模块**: `engine::ConfiguredHandler`、`engine::dispatcher`
- **被引用方**: `session_start`、`user_prompt_submit`、`pre_tool_use`、`post_tool_use`、`stop` 以及 `engine::discovery`

## 实现备注

- `*` 和空字符串被特殊处理为"匹配所有"，而不会被当作正则表达式解析。
- `PreToolUse`、`PostToolUse` 和 `SessionStart` 支持 matcher 过滤；`UserPromptSubmit` 和 `Stop` 始终忽略 matcher。
- 内嵌测试覆盖了 matcher 的各种场景：省略 matcher、星号、空字符串、正则交替、锚定正则和无效正则。
