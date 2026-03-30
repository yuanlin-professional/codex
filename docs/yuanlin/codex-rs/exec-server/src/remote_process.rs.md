# `remote_process.rs` — 远程进程执行后端

## 功能概述

`RemoteProcess` 实现 `ExecBackend` trait，将进程操作通过 `ExecServerClient` 委托到远程 exec-server。启动进程时先注册会话，再发起 exec RPC 调用。`RemoteExecProcess` 实现 `ExecProcess` trait，在 drop 时自动注销会话。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RemoteProcess` | struct | 远程进程执行后端 |
| `RemoteExecProcess` | struct | 远程进程句柄，封装 Session |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start` | `async fn(&self, ExecParams) -> Result<StartedExecProcess>` | 注册会话并启动远程进程 |

## 依赖关系

- **引用的 crate**: `async_trait`, `tracing`
- **被引用方**: `environment.rs`

## 实现备注

- 如果 exec 调用失败，会自动注销已注册的会话。
- Drop 实现使用 `tokio::spawn` 异步注销会话，避免阻塞。
