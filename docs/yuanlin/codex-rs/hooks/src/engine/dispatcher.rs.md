# `dispatcher.rs` -- Hook 处理器的选择与并发执行调度

## 功能概述

本文件实现了 Hook 处理器的核心调度逻辑，包括：根据事件类型和 matcher 选择匹配的处理器、生成"运行中"和"已完成"的 HookRunSummary、并发执行所有匹配的处理器并收集解析结果。它是事件处理流程中连接配置发现与实际命令执行的关键桥梁。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ParsedHandler<T>` | struct (generic) | 泛型解析结果，包含 `completed`（HookCompletedEvent）和类型参数 `data`（事件特定数据） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `select_handlers` | `fn(handlers, event_name, matcher_input) -> Vec<ConfiguredHandler>` | 按事件类型过滤并通过 matcher 匹配选择处理器 |
| `running_summary` | `fn(handler) -> HookRunSummary` | 为运行中的处理器生成初始摘要（状态为 Running） |
| `execute_handlers` | `async fn(shell, handlers, input_json, cwd, turn_id, parse) -> Vec<ParsedHandler<T>>` | 并发执行所有处理器命令并用回调函数解析结果 |
| `completed_summary` | `fn(handler, run_result, status, entries) -> HookRunSummary` | 为已完成的处理器生成最终摘要 |
| `scope_for_event` | `fn(event_name) -> HookScope` | 根据事件类型确定作用域：SessionStart 为 Thread，其余为 Turn |

## 依赖关系

- **引用的 crate**: `futures`（join_all 并发）、`codex_protocol`（HookRunSummary、HookScope 等）、`chrono`
- **引用的内部模块**: `engine::CommandShell`、`engine::ConfiguredHandler`、`engine::command_runner`、`events::common::matches_matcher`
- **被引用方**: 所有 `events` 子模块（session_start、pre_tool_use、post_tool_use、user_prompt_submit、stop）

## 实现备注

- `execute_handlers` 使用 `futures::future::join_all` 并发执行所有命令，不是串行的。
- `select_handlers` 保留声明顺序（display_order），允许重复的 handler（同一命令出现多次）。
- `PreToolUse`/`PostToolUse`/`SessionStart` 使用 matcher 过滤，`UserPromptSubmit`/`Stop` 始终选中所有同类 handler。
- 内嵌测试覆盖了重复 handler、overlapping matcher、正则交替匹配、声明顺序保持等场景。
