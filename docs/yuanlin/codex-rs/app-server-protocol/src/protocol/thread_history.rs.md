# `thread_history.rs` — 线程历史构建器

## 功能概述

实现了从持久化的 `RolloutItem` 序列重建会话线程历史（`Turn` 列表）的完整逻辑。`ThreadHistoryBuilder` 是一个有状态的构建器，逐条处理 rollout 事件项（用户消息、Agent 消息、命令执行、补丁应用、MCP 工具调用、推理、搜索、图片生成、上下文压缩、Hook 等），将它们组织为结构化的 Turn 和 ThreadItem 层级。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ThreadHistoryBuilder` | 结构体 | 线程历史构建器，维护当前 Turn 状态和项目索引 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_turns_from_rollout_items` | `fn build_turns_from_rollout_items(items: &[RolloutItem]) -> Vec<Turn>` | 从 rollout 项列表构建 Turn 列表 |
| `ThreadHistoryBuilder::handle_rollout_item` | （内部方法） | 处理单个 rollout 项并更新构建器状态 |
| `ThreadHistoryBuilder::finish` | （内部方法） | 完成构建并返回所有 Turn |

## 依赖关系

- **引用的 crate**: `codex_protocol` 中大量事件类型、`uuid`, `tracing`
- **被引用方**: `lib.rs` 中 re-export；app-server 中恢复线程历史时使用

## 实现备注

- 文件超过 800 行（含大量测试），是 rollout 事件流到结构化 Turn/Item 层级的桥接核心。
- 处理了各种边界情况：命令中断、补丁审批拒绝、MCP 工具错误、Guardian 审批流程、Hook 执行、上下文压缩等。
- 使用 `PendingTurn` 内部结构跟踪正在构建中的 Turn 状态。
