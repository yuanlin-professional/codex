# `unified_exec.rs` — 统一执行命令处理器

## 功能概述
该文件实现了统一的命令执行工具处理器 `UnifiedExecHandler`，负责处理两种子命令：`exec_command`（启动新进程执行命令）和 `write_stdin`（向已运行进程的标准输入写入数据）。支持 TTY/非 TTY 模式、可配置的 shell 路径和登录模式、工作目录解析、沙箱权限管理，以及 apply_patch 命令拦截。通过 `UnifiedExecProcessManager` 管理进程生命周期。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `UnifiedExecHandler` | struct | 统一执行处理器 |
| `ExecCommandArgs` | struct | `exec_command` 的参数：cmd, workdir, shell, login, tty, yield_time_ms, max_output_tokens, sandbox_permissions, additional_permissions, justification, prefix_rule |
| `WriteStdinArgs` | struct | `write_stdin` 的参数：session_id（进程ID）, chars（输入内容）, yield_time_ms, max_output_tokens |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation) -> Result<ExecCommandToolOutput, FunctionCallError>` | 根据 `tool_name` 分发到 `exec_command` 或 `write_stdin` 处理路径 |
| `is_mutating` | `async fn is_mutating(&self, invocation) -> bool` | 解析命令后通过 `is_known_safe_command` 判断是否为变更操作 |
| `pre_tool_use_payload` | `fn pre_tool_use_payload(&self, invocation) -> Option<PreToolUsePayload>` | 仅为 `exec_command` 生成 pre tool use payload |
| `post_tool_use_payload` | `fn post_tool_use_payload(...) -> Option<PostToolUsePayload>` | 仅为非 TTY 的 `exec_command` 生成 post tool use payload |
| `get_command` | `pub(crate) fn get_command(args, session_shell, shell_mode, allow_login_shell) -> Result<Vec<String>, String>` | 根据 shell 模式（Direct/ZshFork）和参数构建实际命令数组 |
| `emit_unified_exec_tty_metric` | `fn emit_unified_exec_tty_metric(telemetry, tty)` | 发送 TTY 使用指标 |

## 依赖关系
- **引用的 crate**: `async_trait`, `codex_features`, `codex_otel`, `codex_protocol`, `codex_shell_command`, `serde`
- **内部依赖**: `is_safe_command`, `shell`, `unified_exec`（`ExecCommandRequest`, `UnifiedExecContext`, `UnifiedExecProcessManager`, `WriteStdinRequest`）, `tools::context`, `tools::handlers`（`apply_patch`, 权限管理函数）, `tools::registry`, `tools::spec`
- **被引用方**: 工具注册表中注册为 `exec_command` 和 `write_stdin` 工具

## 实现备注
- `exec_command` 流程：解析参数 -> 分配进程 ID -> 构建命令 -> 权限检查 -> apply_patch 拦截 -> 执行命令
- `write_stdin` 流程：解析参数 -> 写入进程标准输入 -> 发送 TerminalInteraction 事件
- 支持两种 shell 模式：`Direct`（使用模型指定或默认 shell）和 `ZshFork`（使用配置的 zsh 路径）
- `exec_command` 默认 yield 时间为 10 秒，`write_stdin` 默认为 250 毫秒
- TTY 模式默认关闭（`default_tty` 返回 false）
- 权限提升在非 `OnRequest` 审批策略下被阻止
- 进程 ID 在拦截 apply_patch 或权限检查失败时会被释放
- TTY 使用情况通过 OpenTelemetry 指标进行跟踪
