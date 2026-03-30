# `pty.rs` — PTY 进程生成

## 功能概述

提供将进程附加到伪终端（PTY）的生成功能。`spawn_process` 是主入口，`spawn_process_portable` 使用 `portable_pty` 库的跨平台实现，`spawn_process_preserving_fds`（Unix 专用）通过 `openpty` 系统调用手动创建 PTY 对以支持保留继承文件描述符。子进程被设置为新会话领导者，PTY slave 成为其控制终端。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `spawn_process` | `async fn(program, args, cwd, env, arg0, size) -> Result<SpawnedProcess>` | 生成 PTY 进程 |
| `spawn_process_with_inherited_fds` | `async fn(..., inherited_fds) -> Result<SpawnedProcess>` | 生成 PTY 进程并保留继承 fd |
| `conpty_supported` | `fn() -> bool` | 检查 ConPTY 可用性 |
| `close_inherited_fds_except` | `fn(preserved_fds)` | 关闭除保留 fd 外的所有继承 fd |

## 依赖关系

- **引用的 crate**: `portable_pty`, `tokio`, `anyhow`, `libc`
- **被引用方**: `pty/src/lib.rs`

## 实现备注

- Unix 下 PTY master/slave 都设置 `FD_CLOEXEC`。
- `close_inherited_fds_except` 遍历 `/dev/fd` 目录并跳过 fd 0-2、保留 fd 和 CLOEXEC fd。
- 读取线程使用 `spawn_blocking` 以避免阻塞 tokio 运行时。
