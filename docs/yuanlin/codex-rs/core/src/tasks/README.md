# tasks/ -- 任务管理

## 功能概述

`tasks/` 负责 Codex 会话中各种异步任务的定义和生命周期管理。每种任务类型封装一个特定的 Codex 工作流（普通对话、代码审查、上下文压缩、撤销、用户 Shell 命令等），通过统一的 `SessionTask` trait 进行抽象。任务由 `Session` 在后台 Tokio 任务中生成和运行，支持取消、中断和优雅终止。

## 目录结构

```
tasks/
├── mod.rs                   # 模块入口，定义 SessionTask trait 和 Session 的任务生命周期方法
├── regular.rs               # RegularTask：普通对话轮次，驱动模型推理 + 工具执行循环
├── compact.rs               # CompactTask：上下文压缩任务，在 token 超限时自动触发
├── review.rs                # ReviewTask：代码审查任务
├── undo.rs                  # UndoTask：撤销操作任务
├── user_shell.rs            # UserShellCommandTask：用户直接执行 Shell 命令的任务
├── ghost_snapshot.rs        # GhostSnapshotTask：中断后分叉快照任务
├── mod_tests.rs             # 模块测试
└── ghost_snapshot_tests.rs  # 快照测试
```

## 依赖关系

- **上游依赖**：`tokio`（异步运行时、CancellationToken）、`async_trait`、`codex-otel`（遥测指标）、`codex-protocol`（事件类型 `EventMsg`、`UserInput`）
- **内部依赖**：`codex/`（`Session`、`TurnContext`）、`state/`（`ActiveTurn`、`RunningTask`、`TaskKind`）、`tools/`（ToolRouter）、`models_manager/`（模型管理）、`hook_runtime`（输入/输出钩子）、`context_manager/`（历史记录管理）
- **被依赖方**：`codex/`（Session::spawn_task 调度任务）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `SessionTask` trait | 异步任务 trait，定义 `kind()`（任务类型）、`span_name()`（追踪名）、`run()`（执行）、`abort()`（清理） |
| `SessionTaskContext` | 任务上下文封装，提供 `clone_session()`、`auth_manager()`、`models_manager()` |
| `RegularTask` | 普通对话任务，驱动模型推理与工具调用的主循环 |
| `CompactTask` | 上下文压缩任务，在 token 超限时压缩历史 |
| `ReviewTask` | 代码审查任务 |
| `UndoTask` | 撤销操作任务 |
| `UserShellCommandTask` | 用户 Shell 命令执行任务 |
| `GhostSnapshotTask` | 中断分叉快照任务 |
| `Session::spawn_task()` | 在 Session 上生成新任务，先中止现有任务再启动 |
| `Session::abort_all_tasks()` | 中止所有活动任务，支持 `Interrupted` 和 `Replaced` 两种原因 |
| `Session::on_task_finished()` | 任务完成回调，发射 `TurnComplete` 事件并记录 token 使用指标 |
