# `session_start.rs` -- 会话启动事件的 Hook 处理

## 功能概述

本文件实现了 `SessionStart` 事件的 Hook 预览与执行逻辑。当 Codex 会话启动（startup）或恢复（resume）时，系统通过此模块选择匹配的 handler 并执行对应的 Hook 命令。命令输出可以注入额外上下文（additional context）给模型，也可以停止会话的启动流程。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SessionStartSource` | enum | 会话启动来源，分为 `Startup`（新启动）和 `Resume`（恢复） |
| `SessionStartRequest` | struct | 请求参数，包含 session_id、cwd、model、permission_mode 和 source |
| `SessionStartOutcome` | struct | 执行结果，包括 hook 事件列表、是否停止、停止原因和额外上下文 |
| `SessionStartHandlerData` | struct (private) | 单个 handler 解析后的中间数据 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `preview` | `fn(handlers, request) -> Vec<HookRunSummary>` | 预览匹配的 handler，使用 source（startup/resume）作为 matcher input |
| `run` | `async fn(handlers, shell, request, turn_id) -> SessionStartOutcome` | 执行所有匹配 handler 并聚合结果 |
| `parse_completed` | `fn(handler, run_result, turn_id) -> ParsedHandler<...>` | 解析命令输出，支持 JSON 和纯文本两种格式 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（HookCompletedEvent 等）、`serde_json`
- **引用的内部模块**: `events::common`、`engine::dispatcher`、`engine::output_parser`、`engine::CommandShell`
- **被引用方**: `engine::mod.rs` 中的 `ClaudeHooksEngine::run_session_start`

## 实现备注

- 与 `UserPromptSubmit` 不同，`SessionStart` 不支持 exit code 2 的阻止机制；非零退出码统一标记为 `Failed`。
- 纯文本 stdout 直接作为 additional context 注入模型。
- 以 `{` 或 `[` 开头但 JSON 解析失败的输出会被标记为 `Failed`，防止恶意 JSON 被误当作普通文本注入。
- 内嵌测试覆盖了纯文本上下文、continue:false、无效 JSON 等场景。
