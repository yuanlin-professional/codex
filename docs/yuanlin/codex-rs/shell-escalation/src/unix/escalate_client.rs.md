# `escalate_client.rs` — execve 包装器客户端

## 功能概述
实现 execve 拦截包装器的客户端逻辑。当 shell 中发生 exec() 调用时，包装器通过继承的 Unix socket 与提升服务端通信，发送 `EscalateRequest` 并根据响应决定：直接执行（Run）、委托服务端执行（Escalate，传递 stdio 文件描述符）或拒绝执行（Deny）。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_shell_escalation_execve_wrapper` | `async fn(file, argv) -> Result<i32>` | 主入口：通过 socket 与服务端协商后执行命令 |

## 依赖关系
- **引用的 crate**: `anyhow`, `libc`, `codex_utils_absolute_path`
- **被引用方**: `execve_wrapper.rs`, `lib.rs`

## 实现备注
Escalate 模式下通过 SCM_RIGHTS 传递 stdin/stdout/stderr 文件描述符；Run 模式下直接调用 libc::execv 替换进程。
