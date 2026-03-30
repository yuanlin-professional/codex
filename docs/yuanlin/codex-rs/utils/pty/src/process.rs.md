# `process.rs` — 进程句柄与生成结果类型

## 功能概述

定义 `ProcessHandle` 结构体，它是驱动交互式进程（PTY 或管道）的核心句柄。提供 stdin 写入、终止子进程、PTY 大小调整、关闭 stdin 等操作。还定义了 `TerminalSize`（终端行列大小）、`SpawnedProcess`（生成结果包含 session/stdout/stderr/exit 通道）、`PtyHandles`（PTY master/slave 句柄保持）以及 `combine_output_receivers`（将 stdout/stderr 合并为单一 broadcast 接收器）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ProcessHandle` | struct | 进程交互句柄 |
| `TerminalSize` | struct | 终端大小（默认 24x80） |
| `SpawnedProcess` | struct | 进程生成结果 |
| `PtyHandles` | struct | PTY master/slave 句柄 |
| `PtyMasterHandle` | enum | PTY master 变体：Resizable 或 Opaque(Unix raw fd) |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `writer_sender` | `fn(&self) -> mpsc::Sender<Vec<u8>>` | 获取 stdin 写入通道 |
| `has_exited` | `fn(&self) -> bool` | 检查子进程是否已退出 |
| `resize` | `fn(&self, size) -> Result<()>` | 调整 PTY 大小 |
| `close_stdin` | `fn(&self)` | 关闭 stdin |
| `request_terminate` | `fn(&self)` | 终止子进程但保留读取任务 |
| `terminate` | `fn(&self)` | 终止子进程并中止所有辅助任务 |
| `combine_output_receivers` | `fn(stdout_rx, stderr_rx) -> broadcast::Receiver<Vec<u8>>` | 合并 stdout/stderr |

## 依赖关系

- **引用的 crate**: `portable_pty`, `tokio`, `anyhow`
- **被引用方**: `pipe.rs`, `pty.rs`, 命令执行引擎

## 实现备注

- `ProcessHandle` 在 `Drop` 时自动调用 `terminate()`。
- Unix 下 `resize_raw_pty` 使用 `TIOCSWINSZ` ioctl。
