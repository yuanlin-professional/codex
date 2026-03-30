# `user_prompt_submit.rs` -- 用户提示提交事件的 Hook 处理

## 功能概述

本文件实现了 `UserPromptSubmit` 事件的 Hook 预览与执行逻辑。当用户提交一条 prompt 时，系统通过此模块筛选匹配的 handler、将请求序列化为 JSON 并通过子进程执行对应的 Hook 命令，最后解析命令输出以决定是否阻止（block）、停止（stop）处理或向模型注入额外上下文（additional context）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UserPromptSubmitRequest` | struct | 包含 session_id、turn_id、cwd、model、prompt 等请求参数 |
| `UserPromptSubmitOutcome` | struct | 执行结果，包括 hook 事件列表、是否停止、停止原因和额外上下文 |
| `UserPromptSubmitHandlerData` | struct (private) | 单个 handler 解析后的中间数据，含 should_stop、stop_reason、additional_contexts |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `preview` | `fn(handlers, request) -> Vec<HookRunSummary>` | 预览匹配的 handler 列表，不实际执行 |
| `run` | `async fn(handlers, shell, request) -> UserPromptSubmitOutcome` | 执行所有匹配的 handler 并聚合结果 |
| `parse_completed` | `fn(handler, run_result, turn_id) -> ParsedHandler<...>` | 解析单个 handler 的命令运行结果，处理 exit code 0/2 及 JSON 输出 |
| `serialization_failure_outcome` | `fn(hook_events) -> UserPromptSubmitOutcome` | 在序列化失败时生成默认的 outcome |

## 依赖关系

- **引用的 crate**: `codex_protocol`（HookCompletedEvent、HookRunSummary 等协议类型）、`serde_json`
- **引用的内部模块**: `events::common`（工具函数）、`engine::dispatcher`（handler 选择与执行）、`engine::output_parser`（JSON 输出解析）、`engine::CommandShell`、`engine::ConfiguredHandler`
- **被引用方**: `engine::mod.rs` 中的 `ClaudeHooksEngine` 调用 `preview` 和 `run`

## 实现备注

- exit code 0 时会尝试解析 JSON 输出；若输出看起来像 JSON 但解析失败，则标记为 `Failed`；若为纯文本则作为 additional context 注入。
- exit code 2 表示被策略阻止（blocked），此时从 stderr 读取阻止原因。
- `continue:false` 的 JSON 输出表示停止处理，但仍然保留 additional context 供后续轮次使用。
- 内嵌测试模块覆盖了 continue_false、block decision、exit code 2 等场景。
