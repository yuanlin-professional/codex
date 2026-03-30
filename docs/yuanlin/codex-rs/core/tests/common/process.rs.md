# `process.rs` — 进程生命周期等待工具

## 功能概述

提供在集成测试中等待外部进程启动和退出的异步工具函数。包括从 PID 文件中读取进程 ID、检查进程是否存活（通过 `kill -0`），以及带超时的进程退出等待。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `wait_for_pid_file` | `async fn(path: &Path) -> Result<String>` | 以 25ms 间隔轮询 PID 文件直到内容非空（2 秒超时），返回 PID 字符串 |
| `process_is_alive` | `fn(pid: &str) -> Result<bool>` | 通过 `kill -0` 探测进程是否存活 |
| `wait_for_process_exit` | `async fn(pid: &str) -> Result<()>` | 以 25ms 间隔轮询进程状态直到退出（2 秒超时） |

## 依赖关系

- **引用的 crate**: `anyhow`、`tokio`、`std::fs`、`std::process::Command`
- **被引用方**: 被需要跟踪外部进程生命周期的集成测试使用
