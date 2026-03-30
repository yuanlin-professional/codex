# `zsh_fork_backend.rs` — Zsh Fork 后端

## 功能概述
为 shell 命令和 unified exec 提供 zsh-fork 执行后端的平台抽象层。在 Unix 上委托给 `unix_escalation` 模块执行，在非 Unix 平台上返回 `None` 表示不支持。还实现了 `SpawnLifecycle` trait 用于管理 escalation session 的 FD 继承和后清理。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `PreparedUnifiedExecSpawn` | struct | 准备好的 unified exec 启动参数：exec_request + spawn_lifecycle |
| `ZshForkSpawnLifecycle` | struct (Unix) | 管理 escalation session 的文件描述符继承和关闭 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `maybe_run_shell_command` | `async fn(req, attempt, ctx, command) -> Result<Option<ExecToolCallOutput>, ToolError>` | 尝试通过 zsh-fork 运行 shell 命令，返回 None 时回退到标准路径 |
| `maybe_prepare_unified_exec` | `async fn(req, attempt, ctx, exec_request, zsh_fork_config) -> Result<Option<PreparedUnifiedExecSpawn>, ToolError>` | 为 unified exec 准备 zsh-fork 启动配置 |

## 依赖关系
- **引用的 crate**: `codex_shell_escalation` (Unix)
- **内部依赖**: `unix_escalation` (Unix), `crate::unified_exec::SpawnLifecycle`
- **被引用方**: `shell.rs`, `unified_exec.rs`

## 实现备注
- 使用条件编译 `#[cfg(unix)]` / `#[cfg(not(unix))]` 实现平台分离
- Unix 实现中 `ZshForkSpawnLifecycle::inherited_fds()` 解析 `CODEX_ESCALATE_SOCKET` 环境变量获取需要继承的 FD
- `after_spawn()` 调用 `close_client_socket()` 关闭客户端 socket
