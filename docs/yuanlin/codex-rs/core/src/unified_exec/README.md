# unified_exec/ -- 统一执行进程管理

## 功能概述

`unified_exec/` 实现了 Codex 的交互式进程执行系统（Unified Exec），将审批、沙箱和 PTY 进程管理整合为一个统一的流程。它支持创建、复用和管理多个交互式进程，提供 stdin 写入、输出缓冲和流式输出，并与 `ToolOrchestrator` 集成实现审批和沙箱选择。核心能力包括：PTY 进程生成、沙箱执行（含拒绝后无沙箱重试）、head-tail 输出缓冲（保留首尾丢弃中间）、异步退出监控和流式输出。

## 目录结构

```
unified_exec/
├── mod.rs                      # 模块入口：常量定义、UnifiedExecProcessManager、ExecCommandRequest、WriteStdinRequest
├── process.rs                  # UnifiedExecProcess：PTY 进程封装、输出缓冲、生命周期管理
├── process_state.rs            # ProcessState：共享退出/失败状态（本地和远程进程）
├── process_manager.rs          # 进程编排：审批、沙箱选择、进程创建/复用、stdin 写入
├── head_tail_buffer.rs         # HeadTailBuffer：保留首尾丢弃中间的有限容量输出缓冲
├── async_watcher.rs            # 异步退出监控和流式输出发射
├── errors.rs                   # UnifiedExecError 错误类型
├── mod_tests.rs                # 模块测试
├── process_tests.rs            # 进程测试
├── process_manager_tests.rs    # 进程管理器测试
├── head_tail_buffer_tests.rs   # 缓冲测试
└── async_watcher_tests.rs      # 异步监控测试
```

## 依赖关系

- **上游依赖**：`codex-exec-server`（ExecProcess、Environment）、`codex-utils-pty`（SpawnedPty、ExecCommandSession）、`codex-sandboxing`（SandboxType）、`codex-network-proxy`（NetworkProxy）
- **内部依赖**：`sandboxing/`（ExecRequest）、`tools/orchestrator`（ToolOrchestrator）、`tools/events`（工具事件发射）、`tools/sandboxing`（审批上下文）、`codex/`（Session、TurnContext）
- **被依赖方**：`tools/handlers/unified_exec.rs`（工具处理器调用 ProcessManager）、`state/`（SessionServices 持有 UnifiedExecProcessManager）、`config/`（默认超时常量）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `UnifiedExecProcessManager` | 进程管理器：`exec_command()`、`write_stdin()`、`terminate_all_processes()` |
| `UnifiedExecProcess` | PTY 进程封装：输出缓冲、流式读取、状态查询 |
| `HeadTailBuffer` | 输出缓冲：保留 head（前 50%）和 tail（后 50%），丢弃中间部分 |
| `ExecCommandRequest` | 执行命令请求：command、process_id、yield_time、workdir、sandbox_permissions 等 |
| `WriteStdinRequest` | stdin 写入请求：process_id、input、yield_time |
| `ProcessStore` | 进程存储：管理活跃进程和保留的进程 ID |
| 关键常量 | `MAX_UNIFIED_EXEC_PROCESSES=64`、`UNIFIED_EXEC_OUTPUT_MAX_BYTES=1MiB`、`MAX_YIELD_TIME_MS=30000` |
