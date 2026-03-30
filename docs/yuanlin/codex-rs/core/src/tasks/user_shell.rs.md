# `user_shell.rs` — 用户 Shell 命令执行任务

## 功能概述

实现了 `UserShellCommandTask` 和 `execute_user_shell_command` 函数，负责执行用户直接输入的 Shell 命令（如通过 `/shell` 指令）。命令在用户的默认 Shell 环境中执行，支持管道、重定向等 Shell 特性，超时设置为 1 小时。执行结果通过 `ExecCommandBegin`/`ExecCommandEnd` 事件流式报告，并根据执行模式（独立 turn 或辅助 turn）决定如何持久化输出。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UserShellCommandMode` | 枚举 | 执行模式：`StandaloneTurn`（独立 turn，发射 TurnStarted/TurnComplete）或 `ActiveTurnAuxiliary`（附属于现有 turn，不重复发射生命周期事件） |
| `UserShellCommandTask` | 结构体 | 用户 Shell 命令任务，持有命令字符串，实现 `SessionTask` trait |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(command: String) -> Self` | 创建新的用户 Shell 命令任务 |
| `run` | `async fn(self: Arc<Self>, session, turn_context, _input, cancellation_token) -> Option<String>` | 以 `StandaloneTurn` 模式执行 `execute_user_shell_command` |
| `execute_user_shell_command` | `pub(crate) async fn(session, turn_context, command, cancellation_token, mode)` | 核心执行逻辑：构建执行环境 → 发射 Begin 事件 → 执行命令 → 处理结果/取消/错误 → 发射 End 事件 → 持久化输出 |
| `persist_user_shell_output` | `async fn(session, turn_context, raw_command, exec_output, mode)` | 根据执行模式持久化输出：独立模式直接写入历史，辅助模式尝试注入到活跃 turn |

## 依赖关系

- **引用的 crate**: `async_trait`、`tokio_util`、`codex_async_utils`（OrCancelExt）、`uuid`、`codex_sandboxing`、`codex_protocol`
- **引用的内部模块**: `crate::exec`（执行请求和输出类型）、`crate::exec_env::create_env`、`crate::parse_command`、`crate::protocol`（事件类型）、`crate::sandboxing::ExecRequest`、`crate::tools`（输出格式化、快照包装）、`crate::user_shell_command`
- **被引用方**: `tasks/mod.rs`（re-export 为 `UserShellCommandTask`、`UserShellCommandMode`、`execute_user_shell_command`）

## 实现备注

- 用户 Shell 命令使用 `SandboxPolicy::DangerFullAccess` 和 `SandboxType::None`，不受沙箱限制。
- 超时设置为 1 小时（`USER_SHELL_TIMEOUT_MS`），远高于普通工具调用。
- 命令通过 `session_shell.derive_exec_args` 使用用户的 login shell 执行，支持 shell 特性。
- 取消时返回 exit_code=-1 和 "command aborted by user" 的错误输出。
- 辅助模式下，输出通过 `inject_response_items` 注入到活跃 turn；若注入失败，回退为直接记录对话历史。
- 独立模式下在记录输出后调用 `ensure_rollout_materialized`，确保 rollout 持久化。
