# `codex/rollout_reconstruction.rs` — 从 Rollout 日志重建会话历史

## 功能概述

该文件实现了从持久化的 Rollout 日志（`RolloutItem` 序列）重建完整会话历史的核心算法。这是会话恢复（resume）和分支（fork）功能的关键基础设施，负责从 rollout 记录中精确还原对话历史、上一轮设置以及上下文引用信息。算法采用从新到旧的反向扫描策略，一旦收集到足够的元数据即可提前终止，避免处理不必要的历史记录。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RolloutReconstruction` | struct | 重建结果，包含历史记录 `history`、上一轮设置 `previous_turn_settings` 和引用上下文 `reference_context_item` |
| `TurnReferenceContextItem` | enum | 三态枚举：`NeverSet`（从未设置）、`Cleared`（已被压缩清除）、`Latest`（最新基线） |
| `ActiveReplaySegment` | struct | 反向扫描中的活跃回放段，携带 turn_id、是否为用户轮次、上轮设置、引用上下文和替换历史基线 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `turn_ids_are_compatible` | `fn(active_turn_id: Option<&str>, item_turn_id: Option<&str>) -> bool` | 判断两个 turn_id 是否兼容（任一为 None 或二者相等） |
| `finalize_active_segment` | `fn(active_segment, ..., pending_rollback_turns)` | 最终确认一个完整的回放段，处理回滚跳过、替换历史基线、上轮设置和引用上下文的收集 |
| `Session::reconstruct_history_from_rollout` | `async fn(&self, turn_context, rollout_items) -> RolloutReconstruction` | 核心重建方法：先反向扫描确定元数据和存活尾部，再正向重放存活的 rollout 项以构建精确历史 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`ResponseItem`、`RolloutItem`、`EventMsg` 等）
- **引用的模块**: `crate::context_manager`（`ContextManager`、`is_user_turn_boundary`）、`crate::compact`（`build_compacted_history`）
- **被引用方**: `Session` 的会话恢复和分支流程

## 实现备注

算法分为两个阶段：

1. **反向扫描阶段**：从最新到最旧遍历 rollout 项，将项聚合到 `ActiveReplaySegment` 中。遇到 `TurnStarted` 时 finalize 当前段。回滚事件（`ThreadRolledBack`）通过 `pending_rollback_turns` 计数器在 finalize 时跳过最新的 N 个用户轮次段。当替换历史基线、上轮设置和引用上下文都已确定时提前退出。

2. **正向重放阶段**：仅处理存活尾部（`rollout_suffix`），逐项重建精确的 `ContextManager` 历史。支持处理新式压缩（带 `replacement_history`）和旧式压缩（无 `replacement_history`，需重新构建）。

对旧式压缩（`replacement_history` 为 `None`）采用保守策略：清除引用上下文项，接受临时的非标准 prompt 形状，因为这类情况已经足够罕见。
