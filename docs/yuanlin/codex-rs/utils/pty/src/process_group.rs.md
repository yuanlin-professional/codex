# `process_group.rs` — 进程组管理辅助函数

## 功能概述

集中处理 OS 特定的进程组管理操作，确保子进程可以被可靠清理。包括：`set_process_group`（将子进程置入独立进程组）、`detach_from_tty`（脱离控制终端开启新会话）、`kill_process_group`/`kill_process_group_by_pid`（SIGKILL 整个进程组）、`terminate_process_group`（SIGTERM 进程组），以及 Linux 专用的 `set_parent_death_signal`（父进程退出时子进程收到 SIGTERM）。非 Unix 平台均为 no-op。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `set_parent_death_signal` | `fn(parent_pid) -> io::Result<()>` | Linux 下设置父进程死亡信号 |
| `detach_from_tty` | `fn() -> io::Result<()>` | 脱离控制终端 |
| `set_process_group` | `fn() -> io::Result<()>` | 置入独立进程组 |
| `kill_process_group_by_pid` | `fn(pid) -> io::Result<()>` | 按 PID 查找并 SIGKILL 进程组 |
| `kill_process_group` | `fn(pgid) -> io::Result<()>` | 直接 SIGKILL 指定进程组 |
| `terminate_process_group` | `fn(pgid) -> io::Result<bool>` | SIGTERM 指定进程组 |
| `kill_child_process_group` | `fn(child) -> io::Result<()>` | SIGKILL tokio 子进程的进程组 |

## 依赖关系

- **引用的 crate**: `tokio`, `libc`
- **被引用方**: `pipe.rs`, `pty.rs`

## 实现备注

- `set_parent_death_signal` 在 `pre_exec` 中调用，并重新检查 `getppid()` 以避免 fork/exec 竞态。
- `detach_from_tty` 在 `setsid` 失败且错误为 EPERM 时回退到 `setpgid`。
