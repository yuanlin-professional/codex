# `process_manager.rs` — 统一执行进程编排与请求处理

## 功能概述

本文件实现了 `UnifiedExecProcessManager` 的核心编排逻辑，包括进程 ID 分配、命令执行、标准输入写入、输出收集、进程状态刷新以及进程淘汰策略。它是审批、沙箱和进程复用逻辑的集中点，协调 `ToolOrchestrator` 完成权限审批和沙箱选择，并通过本地 PTY 或远程 exec-server 启动实际进程。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UNIFIED_EXEC_ENV` | 常量数组 | 统一执行环境注入的 10 个环境变量（如 `NO_COLOR=1`、`TERM=dumb`、`CODEX_CI=1` 等） |
| `PreparedProcessHandles` | 结构体 | 为 `write_stdin` 或 poll 操作准备的借用进程状态 |
| `ProcessStatus` | 枚举 | 进程刷新后的状态：`Alive`（仍在运行）、`Exited`（已退出）、`Unknown`（未找到） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `allocate_process_id` | `async fn(&self) -> i32` | 分配进程 ID：测试模式下确定性递增，生产模式下随机生成 |
| `release_process_id` | `async fn(&self, process_id: i32)` | 释放进程 ID 并注销关联的网络审批 |
| `exec_command` | `async fn(&self, request, context) -> Result<ExecCommandToolOutput, UnifiedExecError>` | 执行命令的完整流程：沙箱审批 → 启动进程 → 流式输出 → 收集初始输出 → 返回结果 |
| `write_stdin` | `async fn(&self, request) -> Result<ExecCommandToolOutput, UnifiedExecError>` | 向已有进程写入标准输入并轮询输出 |
| `open_session_with_exec_env` | `async fn(&self, process_id, env, tty, spawn_lifecycle, environment) -> Result<UnifiedExecProcess, UnifiedExecError>` | 使用执行环境启动进程会话（本地 PTY 或远程 exec-server） |
| `open_session_with_sandbox` | `async fn(&self, request, cwd, context) -> Result<(UnifiedExecProcess, Option<DeferredNetworkApproval>), UnifiedExecError>` | 通过 `ToolOrchestrator` 完成审批和沙箱选择后启动进程 |
| `collect_output_until_deadline` | `async fn(output_buffer, ..., deadline) -> Vec<u8>` | 收集输出直到截止时间或进程退出，支持暂停状态延展 |
| `store_process` | `async fn(&self, process, context, command, ..., transcript)` | 存储活跃进程条目，必要时淘汰旧进程 |
| `refresh_process_state` | `async fn(&self, process_id) -> ProcessStatus` | 刷新并返回进程状态（存活/已退出/未知） |
| `terminate_all_processes` | `async fn(&self)` | 终止所有管理的进程，用于会话清理 |
| `prune_processes_if_needed` | `fn(store) -> Option<ProcessEntry>` | 进程数达到上限时淘汰一个进程 |
| `process_id_to_prune_from_meta` | `fn(meta) -> Option<i32>` | 淘汰策略：优先淘汰已退出的非最近使用进程，否则淘汰最久未使用的进程 |

## 依赖关系

- **引用的 crate**: `rand`、`tokio`、`tokio_util`、`codex_utils_output_truncation`
- **引用的内部模块**: 大量引用 `crate::unified_exec` 下的类型、`crate::exec_env`、`crate::exec_policy`、`crate::tools`（orchestrator、events、network_approval、runtimes）、`crate::sandboxing`
- **被引用方**: `mod.rs`（通过 `UnifiedExecProcessManager` 对外暴露）、工具运行时层

## 实现备注

- 进程 ID 分配在测试模式下使用确定性递增（从 1000 开始），生产模式下使用 1000-100000 范围内的随机数。
- 淘汰策略保护最近使用的 8 个进程不被淘汰，优先淘汰已退出的进程，否则淘汰 LRU（最久未使用）进程。
- `collect_output_until_deadline` 使用 `tokio::select!` 同时等待新输出、进程退出和截止时间，并支持暂停状态下自动延展截止时间。
- 进程退出后有一个短暂的关闭等待上限（`POST_EXIT_CLOSE_WAIT_CAP` = 50ms），避免无限等待输出关闭信号。
- 环境变量注入确保统一执行环境中禁用颜色输出、使用 `cat` 作为分页器等，以适应非交互式输出处理。
