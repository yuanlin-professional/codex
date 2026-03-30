# `hook_runtime.rs` — Hook 生命周期事件运行时

## 功能概述

该文件实现了 Codex Hook 系统的运行时调度层，负责在会话和工具使用的各个生命周期阶段执行用户配置的钩子脚本。支持四种钩子事件：会话启动（`session_start`）、用户提示提交（`user_prompt_submit`）、工具使用前（`pre_tool_use`）、工具使用后（`post_tool_use`）。钩子运行结果可以注入额外上下文到对话中、阻止工具执行、或阻止用户输入的处理。同时负责将钩子的启动和完成事件通知到前端 UI。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `HookRuntimeOutcome` | struct | 钩子运行结果：是否停止 + 附加上下文列表 |
| `PendingInputHookDisposition` | enum | 待处理输入的钩子判定：Accepted(接受) / Blocked(阻止) |
| `PendingInputRecord` | enum | 待记录的输入：UserMessage(用户消息+附加上下文) / ConversationItem(普通对话项) |
| `ContextInjectingHookOutcome` | struct (内部) | 带上下文注入的钩子结果（统一 session_start 和 user_prompt_submit 的输出格式） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_pending_session_start_hooks` | `async fn(sess, turn_context) -> bool` | 运行待执行的会话启动钩子，返回是否应停止 |
| `run_pre_tool_use_hooks` | `async fn(sess, turn_context, tool_use_id, command) -> Option<String>` | 运行工具使用前钩子，返回阻止原因（如有） |
| `run_post_tool_use_hooks` | `async fn(sess, turn_context, ...) -> PostToolUseOutcome` | 运行工具使用后钩子 |
| `run_user_prompt_submit_hooks` | `async fn(sess, turn_context, prompt) -> HookRuntimeOutcome` | 运行用户提示提交钩子 |
| `inspect_pending_input` | `async fn(sess, turn_context, pending_input_item) -> PendingInputHookDisposition` | 检查待处理输入是否被钩子阻止 |
| `record_pending_input` | `async fn(sess, turn_context, pending_input)` | 将通过钩子检查的输入记录到会话历史 |
| `record_additional_contexts` | `async fn(sess, turn_context, contexts)` | 将附加上下文作为 developer 消息注入对话 |

## 依赖关系

- **引用的 crate**: `codex_hooks`（钩子定义和执行引擎）、`codex_protocol`（协议事件类型、TurnItem）
- **被引用方**: Session 主循环在回合开始、工具调用前后、用户输入处理时调用

## 实现备注

- `run_context_injecting_hook` 是一个泛型辅助函数，统一了 `session_start` 和 `user_prompt_submit` 两种需要注入上下文的钩子的执行流程。
- 附加上下文（`additional_contexts`）作为 `DeveloperInstructions` 类型的消息注入对话历史，对模型可见但不显示给用户。
- 每个钩子运行前先通过 `preview_*` 方法获取预览信息并发送 `HookStarted` 事件，运行后发送 `HookCompleted` 事件。
- `hook_permission_mode` 根据审批策略返回 "bypassPermissions" 或 "default"，供钩子脚本判断当前权限模式。
- `inspect_pending_input` 通过 `parse_turn_item` 判断输入是否为用户消息，只有用户消息才会触发 `user_prompt_submit` 钩子。
