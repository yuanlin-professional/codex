# `async_watcher.rs` — 统一执行输出流的异步监控与事件发射

## 功能概述

本文件实现了统一执行（Unified Exec）模块中 PTY 进程输出的异步流式读取和事件发射功能。它负责在后台任务中持续从 PTY 读取输出数据，将数据追加到共享的转录缓冲区中，并在 UTF-8 边界上发射 `ExecCommandOutputDelta` 事件。同时，当进程退出时，它会聚合所有输出并发射最终的 `ExecCommandEnd` 事件。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TRAILING_OUTPUT_GRACE` | 常量 | 进程退出后等待尾部输出的宽限期（100ms） |
| `UNIFIED_EXEC_OUTPUT_DELTA_MAX_BYTES` | 常量 | 单次 `ExecCommandOutputDelta` 事件负载的最大字节数（8192） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_streaming_output` | `fn(process, context, transcript)` | 启动后台任务，持续从 PTY 读取输出，追加到共享转录缓冲区并发射增量输出事件 |
| `spawn_exit_watcher` | `fn(process, session_ref, turn_ref, call_id, command, cwd, process_id, transcript, started_at)` | 启动后台监控任务，等待 PTY 退出后发射带有聚合转录内容的 `ExecCommandEnd` 事件 |
| `process_chunk` | `async fn(pending, transcript, call_id, session_ref, turn_ref, emitted_deltas, chunk)` | 处理单个输出块：拆分有效 UTF-8 前缀，更新转录缓冲区，发射增量事件 |
| `emit_exec_end_for_unified_exec` | `async fn(session_ref, turn_ref, call_id, command, cwd, process_id, transcript, fallback_output, exit_code, duration)` | 发射正常结束的执行命令结束事件 |
| `emit_failed_exec_end_for_unified_exec` | `async fn(session_ref, turn_ref, call_id, command, cwd, process_id, transcript, message, duration)` | 发射失败时的执行命令结束事件 |
| `split_valid_utf8_prefix` | `fn(buffer) -> Option<Vec<u8>>` | 从字节缓冲区中分离有效的 UTF-8 前缀 |
| `split_valid_utf8_prefix_with_max` | `fn(buffer, max_bytes) -> Option<Vec<u8>>` | 从字节缓冲区中分离不超过指定最大字节数的有效 UTF-8 前缀 |
| `resolve_aggregated_output` | `async fn(transcript, fallback) -> String` | 从转录缓冲区解析聚合输出，若为空则返回 fallback |

## 依赖关系

- **引用的 crate**: `tokio`（异步运行时、同步原语）、`std`（路径、同步）
- **引用的内部模块**: `super::UnifiedExecContext`、`super::process::UnifiedExecProcess`、`crate::codex::Session`/`TurnContext`、`crate::exec`（ExecToolCallOutput、StreamOutput）、`crate::protocol`（EventMsg、ExecCommandOutputDeltaEvent 等）、`crate::tools::events`（ToolEmitter 等）、`crate::unified_exec::head_tail_buffer::HeadTailBuffer`
- **被引用方**: `process_manager.rs`（调用 `start_streaming_output`、`spawn_exit_watcher`、`emit_exec_end_for_unified_exec`、`emit_failed_exec_end_for_unified_exec`）

## 实现备注

- 使用 `tokio::select!` 实现多路复用：同时监听进程退出信号、宽限期超时以及新的输出数据到达。
- 进程退出后会启动一个宽限期（`TRAILING_OUTPUT_GRACE` = 100ms），确保收集到所有尾部输出后再通知输出已排空。
- UTF-8 分割算法会尝试在 UTF-8 边界处切割数据；如果无法找到有效的 UTF-8 前缀（例如遇到无效字节），会逐字节输出以保证流的持续推进。
- 每次调用最多发射 `MAX_EXEC_OUTPUT_DELTAS_PER_CALL` 个增量事件，超出后仍会更新转录缓冲区但不再发射事件。
