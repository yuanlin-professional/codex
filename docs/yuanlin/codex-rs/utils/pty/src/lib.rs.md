# `lib.rs` — PTY/管道进程生成入口

## 功能概述

`pty` crate 的入口模块，提供两种进程生成方式的统一接口：PTY（伪终端）和管道（pipe）。导出核心类型 `ProcessHandle`、`SpawnedProcess`、`TerminalSize`，以及 `spawn_pty_process`、`spawn_pipe_process` 等生成函数。定义默认输出字节上限 `DEFAULT_OUTPUT_BYTES_CAP`（1MB），并为向后兼容保留 `ExecCommandSession` 和 `SpawnedPty` 别名。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ProcessHandle` | struct | 进程交互句柄（读写/终止/调整大小） |
| `SpawnedProcess` | struct | 包含 session、stdout_rx、stderr_rx、exit_rx 的生成结果 |
| `TerminalSize` | struct | 终端行列大小 |

## 依赖关系

- **引用的 crate**: `portable_pty`, `tokio`, `anyhow`
- **被引用方**: 命令执行引擎

## 实现备注

- Windows 使用 ConPTY，其他平台使用 native PTY。
- `conpty_supported()` 在非 Windows 平台始终返回 `true`。
