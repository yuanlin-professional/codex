# `shell.rs` — Shell 运行时

## 功能概述
实现了 `ShellRuntime`，在编排器下执行 shell 命令。负责审批请求、构建沙箱转换输入，并在当前沙箱策略下运行。支持三种后端模式：Generic（通用）、ShellCommandClassic（传统 shell_command）和 ShellCommandZshFork（zsh-fork 模式）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ShellRequest` | struct | Shell 请求：command、cwd、timeout、env、network、sandbox 配置等 |
| `ShellRuntimeBackend` | enum | 后端选择：Generic / ShellCommandClassic / ShellCommandZshFork |
| `ShellRuntime` | struct | Shell 执行运行时 |
| `ApprovalKey` | struct | 审批缓存键：command、cwd、sandbox_permissions、additional_permissions |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_approval_async` | `fn start_approval_async(req, ctx) -> BoxFuture<ReviewDecision>` | 异步审批：支持 Guardian 和会话级缓存 |
| `run` | `async fn run(req, attempt, ctx)` | 执行 shell 命令，先应用快照包装和 PowerShell UTF-8 前缀，ZshFork 模式下尝试 zsh-fork 执行 |
| `network_approval_spec` | `fn network_approval_spec(req, ctx) -> Option<NetworkApprovalSpec>` | 网络审批规格（Immediate 模式） |

## 依赖关系
- **子模块**: `unix_escalation`, `zsh_fork_backend`
- **引用的 crate**: `codex_sandboxing`, `codex_network_proxy`, `futures`
- **被引用方**: `tools/handlers/shell.rs`, `tools/handlers/shell_command.rs`

## 实现备注
- 命令审批前通过 `canonicalize_command_for_approval` 规范化命令行
- PowerShell 命令会自动添加 UTF-8 编码前缀
- ZshFork 后端在条件不满足时自动回退到标准执行路径
