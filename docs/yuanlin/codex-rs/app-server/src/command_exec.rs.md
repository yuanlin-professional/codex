# `command_exec.rs` — 命令执行会话管理器（PTY/管道/流式 I/O）

## 功能概述

本模块实现了 `CommandExecManager`，管理通过 `command/exec` API 创建的子进程会话。它支持三种进程模式：PTY 终端模式、管道模式（带 stdin 流式输入）、管道模式（无 stdin）。每个会话通过 `ConnectionProcessId` 唯一标识并绑定到特定连接。管理器支持流式 stdout/stderr 输出（通过 base64 编码的增量通知推送到客户端）、stdin 写入、终端窗口大小调整、进程终止、超时处理以及连接断开时的自动清理。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CommandExecManager` | 结构体 | 管理所有活跃的命令执行会话 |
| `CommandExecSession` | 枚举 | `Active`（正常会话）或 `UnsupportedWindowsSandbox`（Windows 沙箱降级） |
| `StartCommandExecParams` | 结构体 | 启动命令执行的参数包 |
| `ConnectionProcessId` | 结构体 | 连接 ID + 进程 ID 的复合键 |
| `InternalProcessId` | 枚举 | `Generated`（服务端分配）或 `Client`（客户端指定） |
| `CommandControl` | 枚举 | 控制命令：`Write`、`Resize`、`Terminate` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start` | `async fn(&self, params) -> Result<(), JSONRPCErrorError>` | 启动新的命令执行会话 |
| `write` | `async fn(&self, request_id, params) -> Result<WriteResponse, ...>` | 向进程 stdin 写入数据 |
| `terminate` | `async fn(&self, request_id, params) -> Result<TerminateResponse, ...>` | 终止指定进程 |
| `resize` | `async fn(&self, request_id, params) -> Result<ResizeResponse, ...>` | 调整 PTY 终端大小 |
| `connection_closed` | `async fn(&self, connection_id)` | 连接断开时终止该连接的所有进程 |
| `run_command` | `async fn(params)` | 内部函数：运行进程主循环，处理 I/O 和超时 |

## 依赖关系

- **引用的 crate**: `codex_utils_pty`（`SpawnedProcess`、`ProcessHandle`）、`codex_core::exec`（超时/沙箱）、`codex_sandboxing`、`base64`
- **被引用方**: `codex_message_processor.rs`（处理 `command/exec` 相关请求时调用）

## 实现备注

- Windows 沙箱模式不支持流式 I/O，会走单独的同步执行路径。
- 输出通过 `spawn_process_output` 异步任务收集，支持可配置的字节上限（`output_bytes_cap`）。
- 输出块会合并小于 64KiB 的连续块以减少通知次数。
- 包含丰富的单元测试，覆盖 Windows 沙箱拒绝、非流式执行、取消超时、控制命令失败等场景。
