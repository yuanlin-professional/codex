# `process.rs` — 统一执行进程封装与生命周期管理

## 功能概述

本文件实现了 `UnifiedExecProcess`，它是对本地 PTY 会话和远程 exec-server 进程的统一抽象封装。它管理进程的完整生命周期：创建、输出收集与广播、写入标准输入、退出检测和终止。通过 `SpawnLifecycle` trait 支持自定义的进程启动前后钩子，并提供沙箱拒绝检测机制。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SpawnLifecycle` | trait | 进程启动生命周期钩子，提供 `inherited_fds`（继承的文件描述符）和 `after_spawn`（启动后回调） |
| `SpawnLifecycleHandle` | 类型别名 | `Box<dyn SpawnLifecycle>`，动态分发的启动生命周期句柄 |
| `NoopSpawnLifecycle` | 结构体 | 空操作的默认启动生命周期实现 |
| `OutputBuffer` | 类型别名 | `Arc<Mutex<HeadTailBuffer>>`，线程安全的输出缓冲区 |
| `OutputHandles` | 结构体 | 暴露给轮询和流式消费者的共享输出状态句柄 |
| `ProcessHandle` | 枚举 | 传输层特定的进程句柄：`Local`（本地 PTY）或 `Remote`（远程 exec-server） |
| `UnifiedExecProcess` | 结构体 | 统一的进程封装，包含进程句柄、广播通道、输出缓冲区、取消令牌、状态通道等 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(process_handle, sandbox_type, spawn_lifecycle) -> Self` | 内部构造器，初始化所有共享状态和通道 |
| `write` | `async fn(&self, data: &[u8]) -> Result<(), UnifiedExecError>` | 向进程标准输入写入数据，支持本地和远程两种模式 |
| `output_handles` | `fn(&self) -> OutputHandles` | 返回共享输出状态的句柄，供外部轮询使用 |
| `has_exited` | `fn(&self) -> bool` | 检查进程是否已退出 |
| `exit_code` | `fn(&self) -> Option<i32>` | 获取进程退出码 |
| `terminate` | `fn(&self)` | 终止进程并取消所有关联的异步任务 |
| `check_for_sandbox_denial` | `async fn(&self) -> Result<(), UnifiedExecError>` | 检查进程输出中是否存在沙箱拒绝信号 |
| `from_spawned` | `async fn(spawned, sandbox_type, spawn_lifecycle) -> Result<Self, UnifiedExecError>` | 从本地 PTY 创建进程实例，启动输出收集任务并检测早期退出 |
| `from_remote_started` | `async fn(started, sandbox_type) -> Result<Self, UnifiedExecError>` | 从远程 exec-server 创建进程实例，启动远程输出拉取任务 |
| `spawn_local_output_task` | `fn(...) -> JoinHandle<()>` | 启动本地输出收集后台任务 |
| `spawn_remote_output_task` | `fn(...) -> JoinHandle<()>` | 启动远程输出轮询后台任务 |
| `signal_exit` | `fn(&self, exit_code: Option<i32>)` | 内部方法，标记进程已退出并取消令牌 |

## 依赖关系

- **引用的 crate**: `tokio`（异步原语）、`tokio_util`（CancellationToken）、`codex_exec_server`（远程执行接口）、`codex_sandboxing`（沙箱类型）、`codex_utils_pty`（PTY 会话）、`codex_utils_output_truncation`（截断工具）、`codex_protocol`（截断策略）
- **引用的内部模块**: `super::UnifiedExecError`、`super::head_tail_buffer::HeadTailBuffer`、`super::process_state::ProcessState`
- **被引用方**: `process_manager.rs`（创建和管理进程）、`async_watcher.rs`（读取进程输出）、`mod.rs`（re-export）

## 实现备注

- 本地进程通过 `broadcast` 通道将 PTY 输出广播给多个消费者（流式事件发射器和输出缓冲区）。
- 远程进程通过轮询 `read` 接口拉取输出，使用 `wake` 通道感知新数据到达。
- 进程创建后有 150ms 的早期退出宽限期（`EARLY_EXIT_GRACE_PERIOD`），用于检测立即失败的命令。
- `Drop` 实现会自动调用 `terminate`，确保进程资源在对象销毁时被清理。
- 沙箱拒绝检测使用共享的 `is_likely_sandbox_denied` 启发式算法，与其他执行路径保持一致。
