# `pipe.rs` — 基于管道的进程生成

## 功能概述

使用标准 stdio 管道（非 PTY）生成非交互式子进程。支持两种模式：`Piped`（stdin 可写）和 `Null`（stdin 立即关闭）。Unix 下子进程通过 `setsid` 脱离控制终端并置入独立进程组，支持 Linux 上的 `PR_SET_PDEATHSIG` 父进程死亡信号。可选保留指定的继承文件描述符。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `spawn_process` | `async fn(program, args, cwd, env, arg0) -> Result<SpawnedProcess>` | 生成管道进程（stdin 可写） |
| `spawn_process_no_stdin` | `async fn(program, args, cwd, env, arg0) -> Result<SpawnedProcess>` | 生成管道进程（stdin 关闭） |
| `spawn_process_no_stdin_with_inherited_fds` | `async fn(..., inherited_fds) -> Result<SpawnedProcess>` | 生成管道进程并保留继承 fd |

## 依赖关系

- **引用的 crate**: `tokio`, `anyhow`
- **被引用方**: `pty/src/lib.rs`

## 实现备注

- Unix 下终止使用 `kill_process_group`（SIGKILL），Windows 使用 `TerminateProcess`。
- 输出通过 8KB 缓冲区的异步读取循环收集。
