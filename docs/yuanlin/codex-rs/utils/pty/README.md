# pty

## 功能概述

`codex-utils-pty` 提供了跨平台的伪终端（PTY）和管道（pipe）进程创建与管理功能。支持两种进程启动模式：
- **PTY 模式** -- 通过伪终端运行交互式进程
- **Pipe 模式** -- 通过标准管道运行非交互式进程

提供统一的 `ProcessHandle` / `SpawnedProcess` 接口来管理进程的输入/输出、终端大小调整和进程组操作。

## 目录结构

```
pty/
├── Cargo.toml
└── src/
    ├── lib.rs             # 模块导出与类型别名
    ├── pipe.rs            # 管道模式进程启动
    ├── process.rs         # ProcessHandle / SpawnedProcess 类型
    ├── process_group.rs   # 进程组管理
    ├── pty.rs             # PTY 模式进程启动
    ├── tests.rs           # 测试
    └── win/               # Windows 特定实现（ConPTY）
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `anyhow` | 错误处理 |
| `portable-pty` | 跨平台 PTY 抽象 |
| `tokio` | 异步 I/O 和进程管理 |
| `libc`（Unix） | Unix 系统调用 |
| `winapi`（Windows） | Windows API 调用 |

## 核心接口/API

### 常量

```rust
pub const DEFAULT_OUTPUT_BYTES_CAP: usize = 1024 * 1024;  // 默认输出捕获上限 1MB
```

### 进程启动函数

```rust
// PTY 模式启动进程
pub fn spawn_pty_process(...) -> ...;

// 管道模式启动进程
pub fn spawn_pipe_process(...) -> ...;

// 管道模式启动并立即关闭 stdin
pub fn spawn_pipe_process_no_stdin(...) -> ...;
```

### `ProcessHandle`（别名 `ExecCommandSession`）

管理已启动进程的句柄，用于与进程交互。

### `SpawnedProcess`（别名 `SpawnedPty`）

包含进程句柄、分离的 stdout/stderr 接收器和退出状态接收器的完整包。

### `TerminalSize`

终端尺寸（字符行列数），用于 PTY 创建和调整大小。

### `combine_output_receivers`

将 stdout 和 stderr 接收器合并为单个广播接收器。

### `conpty_supported() -> bool`

检测当前平台是否支持 ConPTY（仅 Windows 相关）。
