# `mod.rs` -- Code Mode V8 运行时核心

## 功能概述

本模块是 Code Mode 的 V8 JavaScript 运行时核心。它管理 V8 Isolate 的生命周期，在独立线程中执行 JavaScript 代码，通过异步通道与主线程通信。支持工具调用（通过 Promise 挂起/恢复）、执行结果收集、以及通过 `IsolateHandle` 从外部终止长时间运行的脚本。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecuteRequest` | struct | exec 执行请求：工具调用 ID、启用工具列表、源码、存储值、超时和 token 限制 |
| `WaitRequest` | struct | wait 请求：cell ID、等待超时、是否终止 |
| `RuntimeResponse` | enum | 运行时响应：`Yielded`（挂起）、`Terminated`（终止）、`Result`（完成） |
| `TurnMessage` | enum | 运行时向主线程的消息：`ToolCall`（工具调用）、`Notify`（通知） |
| `RuntimeCommand` | enum | 主线程向运行时的命令：`ToolResponse`、`ToolError`、`Terminate` |
| `RuntimeEvent` | enum | 运行时事件：`Started`、`ContentItem`、`YieldRequested`、`ToolCall`、`Notify`、`Result` |
| `RuntimeState` | struct | V8 slot 状态：事件发送器、待处理工具调用、存储值、工具元数据等 |
| `CompletionState` | enum | 完成状态：`Pending` 或 `Completed` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `spawn_runtime` | `pub fn spawn_runtime(request, event_tx) -> Result<(Sender<RuntimeCommand>, IsolateHandle), String>` | 在新线程中启动 V8 运行时，返回命令发送器和终止句柄 |
| `run_runtime` | `fn run_runtime(config, event_tx, command_rx, isolate_handle_tx)` | 运行时主循环：初始化 V8、执行模块、处理工具调用循环 |
| `initialize_v8` | `fn initialize_v8()` | 使用 `OnceLock` 确保 V8 平台全局只初始化一次 |

## 依赖关系

- **引用的 crate**: `v8`, `tokio::sync::mpsc`, `serde_json`
- **被引用方**: `service.rs`（通过 CodeModeService 使用）

## 实现备注

- V8 运行时在独立的 OS 线程中运行（`thread::spawn`），与 Tokio async 运行时通过 `mpsc` 通道通信
- 工具调用通过 V8 Promise 实现：JavaScript 调用工具方法时挂起 Promise，Rust 侧收到响应后通过 resolver 恢复
- `IsolateHandle` 可用于从外部安全终止 CPU 密集型脚本
- 使用 `EXIT_SENTINEL` 特殊字符串标记 `exit()` 调用的早期退出
