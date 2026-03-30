# `unified_exec.rs` — Unified Exec 运行时

## 功能概述
处理统一执行（unified exec）请求的审批和沙箱编排，将进程启动委托给 `UnifiedExecProcessManager`。支持 PTY 会话、zsh-fork 后端、网络审批（Deferred 模式）和 Guardian 审批流程。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `UnifiedExecRequest` | struct | 执行请求：command、process_id、cwd、env、tty、网络、沙箱配置 |
| `UnifiedExecApprovalKey` | struct | 审批缓存键：command、cwd、tty、sandbox_permissions |
| `UnifiedExecRuntime<'a>` | struct | 运行时适配器，绑定到 `UnifiedExecProcessManager` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run` | `async fn run(req, attempt, ctx) -> Result<UnifiedExecProcess, ToolError>` | 核心执行逻辑：应用快照包装、尝试 zsh-fork、构建沙箱命令、启动 PTY 会话 |
| `start_approval_async` | `fn(req, ctx) -> BoxFuture<ReviewDecision>` | 异步审批，支持 Guardian 和会话缓存 |
| `network_approval_spec` | `fn(req, ctx) -> Option<NetworkApprovalSpec>` | 网络审批规格（Deferred 模式） |

## 依赖关系
- **引用的 crate**: `codex_network_proxy`, `futures`, `codex_sandboxing`
- **内部依赖**: `crate::unified_exec`, `crate::tools::runtimes::shell::zsh_fork_backend`
- **被引用方**: `tools/handlers/unified_exec.rs`

## 实现备注
- 与 `ShellRuntime` 类似但使用 Deferred 网络审批模式
- ZshFork 模式下不支持 `exec_server_url` 配置
- 回退路径使用 `NoopSpawnLifecycle`
- SandboxDenied 错误会被映射为 `ToolError::Codex`
