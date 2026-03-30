# `commit_tick.rs` — Commit tick 排出编排

## 功能概述

本模块桥接队列分块策略（`chunking`）和具体的流控制器（`controller`），编排 commit-tick 的排出流程。调用者提供当前控制器和 tick 范围；本模块计算队列压力、选择排出计划、应用计划并返回生成的历史记录单元格。它保持排出顺序（仅从控制器队列头部排出），不调度 tick 也不直接修改 UI 状态。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_commit_tick` | `(...)  -> CommitTickOutput` | 主流程：快照 -> 计划 -> 应用 -> 输出 |
| `stream_queue_snapshot` | `(...) -> QueueSnapshot` | 从控制器收集队列压力快照 |
| `apply_commit_tick_plan` | `(...) -> CommitTickOutput` | 将排出计划应用到控制器 |

## 依赖关系

- **引用的 crate**: `crate::history_cell`
- **被引用方**: `ChatWidget` 的 commit tick 处理
