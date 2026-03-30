# `unix_escalation.rs` — Unix 权限提升执行

## 功能概述
实现了 Unix 平台上通过 zsh-fork + `codex-shell-escalation` 适配器的权限提升执行机制。该模块在沙箱策略允许时，将 shell 命令交由 escalation server 处理，支持逐命令（execve 级别）的策略评估和审批。这是 Codex 安全执行模型中最核心的策略执行组件之一，约 940 行。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `CoreShellActionProvider` | struct | 实现 `EscalationPolicy`，连接 exec policy 评估与用户审批流程 |
| `CoreShellCommandExecutor` | struct | 实现 `ShellCommandExecutor`，执行沙箱化命令和准备提权执行 |
| `ParsedShellCommand` | struct | 解析后的 shell 命令：program、script、login 标志 |
| `InterceptedExecPolicyContext` | struct | execve 拦截的策略上下文 |
| `CandidateCommands` | struct | 策略评估用的候选命令列表 |
| `PreparedUnifiedExecZshFork` | struct | 为 unified exec 准备的 zsh-fork 执行请求 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `try_run_zsh_fork` | `async fn try_run_zsh_fork(req, attempt, ctx, command)` | Shell 命令的 zsh-fork 执行入口 |
| `prepare_unified_exec_zsh_fork` | `async fn prepare_unified_exec_zsh_fork(req, attempt, ctx, exec_request, ...)` | 为 unified exec 准备 zsh-fork 会话 |
| `evaluate_intercepted_exec_policy` | `fn(policy, program, argv, context) -> Evaluation` | 评估拦截的 execve 调用的策略决策 |
| `CoreShellActionProvider::determine_action` | `async fn determine_action(program, argv, workdir) -> EscalationDecision` | 核心策略决策：Allow/Prompt/Forbidden |
| `extract_shell_script` | `fn(command) -> Result<ParsedShellCommand, ToolError>` | 从命令行中提取 shell 脚本和 `-c`/`-lc` 标志 |
| `map_exec_result` | `fn(sandbox, result) -> Result<ExecToolCallOutput, ToolError>` | 将执行结果映射为工具输出，检测超时和沙箱拒绝 |

## 依赖关系
- **引用的 crate**: `codex_shell_escalation`, `codex_execpolicy`, `codex_sandboxing`, `codex_shell_command`
- **内部依赖**: `crate::tools::sandboxing`, `crate::guardian`, `crate::exec`
- **被引用方**: `zsh_fork_backend.rs`

## 实现备注
- Shell wrapper 解析默认禁用（`ENABLE_INTERCEPTED_EXEC_POLICY_SHELL_WRAPPER_PARSING = false`），依赖 execve 拦截进行权威路径解析
- 预审批的 additional_permissions 在审批决策中降级为 `UseDefault`，避免重复审批
- `EscalationExecution` 支持三种模式：TurnDefault（使用 turn 沙箱）、Unsandboxed（无沙箱）、Permissions（自定义权限）
- 使用 `Stopwatch` 暂停计时器等待用户审批
