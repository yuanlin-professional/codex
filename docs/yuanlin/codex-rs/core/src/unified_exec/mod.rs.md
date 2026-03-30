# `mod.rs` — 统一执行模块入口与核心类型定义

## 功能概述

本文件是 `unified_exec` 模块的入口文件，定义了统一执行（Unified Exec）系统的核心类型和常量。统一执行模块负责管理交互式进程的创建、复用和输出缓冲，结合审批流程和沙箱机制。它通过共享的 `ToolOrchestrator` 处理审批、沙箱选择和重试语义。模块内部分为进程生命周期管理（`process.rs`）、共享退出/失败状态（`process_state.rs`）和编排层（`process_manager.rs`）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UnifiedExecContext` | 结构体 | 统一执行的上下文信息，包含 `Session`、`TurnContext` 和 `call_id` |
| `ExecCommandRequest` | 结构体 | 执行命令请求，包含命令、进程 ID、yield 超时、工作目录、网络代理、沙箱权限等 |
| `WriteStdinRequest` | 结构体 | 向进程标准输入写入数据的请求，包含进程 ID、输入内容、yield 超时 |
| `ProcessStore` | 结构体 | 进程存储，内部使用 `HashMap` 管理活跃进程条目和已预留的进程 ID 集合 |
| `UnifiedExecProcessManager` | 结构体 | 统一执行进程管理器，持有进程存储和最大写入超时配置 |
| `ProcessEntry` | 结构体 | 单个进程条目，包含进程句柄、调用 ID、命令、TTY 标志、网络审批 ID 等 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `clamp_yield_time` | `fn(yield_time_ms: u64) -> u64` | 将 yield 超时限制在 `MIN_YIELD_TIME_MS` 到 `MAX_YIELD_TIME_MS` 范围内 |
| `resolve_max_tokens` | `fn(max_tokens: Option<usize>) -> usize` | 解析最大 token 数，默认为 `DEFAULT_MAX_OUTPUT_TOKENS` |
| `generate_chunk_id` | `fn() -> String` | 生成 6 位随机十六进制 chunk ID |
| `set_deterministic_process_ids_for_tests` | `fn(enabled: bool)` | 测试辅助：启用/禁用确定性进程 ID 生成 |

## 依赖关系

- **引用的 crate**: `codex_network_proxy`、`codex_protocol`、`rand`、`tokio`
- **引用的内部模块**: `crate::codex::Session`/`TurnContext`、`crate::sandboxing::SandboxPermissions`
- **子模块**: `async_watcher`、`errors`、`head_tail_buffer`、`process`、`process_manager`、`process_state`
- **被引用方**: 被 `crate` 中的工具运行时和命令执行路径广泛引用

## 实现备注

- 关键常量：`MIN_YIELD_TIME_MS`（250ms）、`MAX_YIELD_TIME_MS`（30s）、`DEFAULT_MAX_BACKGROUND_TERMINAL_TIMEOUT_MS`（5 分钟）、`UNIFIED_EXEC_OUTPUT_MAX_BYTES`（1 MiB）、`MAX_UNIFIED_EXEC_PROCESSES`（64）。
- 当活跃进程数达到 `WARNING_UNIFIED_EXEC_PROCESSES`（60）时向模型发出警告。
- `UnifiedExecProcessManager::new` 会确保最大写入超时不低于 `MIN_EMPTY_YIELD_TIME_MS`（5 秒）。
