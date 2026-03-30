# `local_process.rs` — 本地进程执行后端

## 功能概述

`LocalProcess` 实现 `ExecBackend` trait，在本地通过 PTY 或 pipe 方式启动子进程。管理进程的输出缓冲（环形缓冲区，上限 1MB/进程）、退出状态跟踪和生命周期通知（output_delta、exited、closed）。支持 long-poll 模式的读取和 PTY 写入。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `LocalProcess` | struct | 本地进程执行后端，管理多个进程 |
| `RunningProcess` | struct | 单个运行中进程的状态（输出缓冲、退出码、打开流数量等） |
| `ProcessEntry` | enum | 进程条目：Starting 或 Running |
| `LocalExecProcess` | struct | 实现 ExecProcess trait 的本地进程句柄 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_process` | `async fn(&self, ExecParams) -> Result<(ExecResponse, wake_tx)>` | 启动进程并注册输出/退出监听 |
| `exec_read` | `async fn(&self, ReadParams) -> Result<ReadResponse>` | 带 long-poll 的输出读取 |
| `exec_write` | `async fn(&self, WriteParams) -> Result<WriteResponse>` | 向 PTY 进程写入 |
| `stream_output` | `async fn(...)` | 异步接收子进程输出并发送通知 |
| `watch_exit` | `async fn(...)` | 监听进程退出并发送 exited/closed 通知 |

## 依赖关系

- **引用的 crate**: `codex_utils_pty`, `tokio`, `async_trait`
- **被引用方**: `environment.rs`, `server/process_handler.rs`

## 实现备注

- 输出缓冲使用 `VecDeque` 实现 FIFO 淘汰，超出 1MB 时丢弃最旧数据。
- 进程退出后保留 30 秒（测试中 25ms），之后自动清理。
- 非 TTY 模式不支持写入，返回 `StdinClosed` 状态。
