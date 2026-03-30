# `escalate_server.rs` — 权限提升服务端

## 功能概述
实现 shell 命令权限提升的服务端。通过 Unix 数据报 socket 监听来自 execve 包装器的握手消息，为每个请求创建流式 socket 会话，根据 `EscalationPolicy` 决策后执行 Run/Escalate/Deny 操作。Escalate 时通过 SCM_RIGHTS 接收 stdio fd 并 spawn 子进程执行命令。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ShellCommandExecutor` | trait | 命令执行适配器，让调用方控制进程生成和沙箱集成 |
| `ExecParams` | 结构体 | 命令执行参数：command, workdir, timeout_ms, login |
| `ExecResult` | 结构体 | 执行结果：exit_code, stdout, stderr, duration, timed_out |
| `PreparedExec` | 结构体 | 准备好的提升命令：command, cwd, env, arg0 |
| `EscalationSession` | 结构体 | 提升会话：env overlay、后台任务、client socket |
| `EscalateServer` | 结构体 | 提升服务端：shell 路径、execve 包装器路径、策略 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `exec` | `async fn(&self, params, cancel, executor) -> Result<ExecResult>` | 启动会话并执行命令 |
| `start_session` | `fn(&self, cancel, executor) -> Result<EscalationSession>` | 启动提升会话并返回环境变量 overlay |

## 依赖关系
- **引用的 crate**: `tokio`, `socket2`, `anyhow`, `codex_utils_absolute_path`
- **被引用方**: `codex-core` 沙箱命令执行器

## 实现备注
包含详尽的测试：Run 决策、Escalate 决策（含 fd 重叠场景）、权限传递、会话关闭时子进程清理等。
