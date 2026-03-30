# `chunking.rs` — 自适应流式分块策略

## 功能概述

本模块实现 commit 动画 tick 的自适应流式分块策略。在 `Smooth` 模式下，每个基线 tick 排出一行队列内容；当队列压力升高时切换到 `CatchUp` 模式，立即排空积压内容以尽快收敛显示延迟。策略是源无关的，仅依赖队列深度和队列年龄。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ChunkingMode` | enum | 分块模式：Smooth（平滑）和 CatchUp（追赶） |
| `AdaptiveChunkingPolicy` | struct | 自适应分块策略状态机 |
| `QueueSnapshot` | struct | 队列压力快照（深度 + 最旧条目年龄） |
| `ChunkingDecision` | enum | 分块决策：Smooth 或 CatchUp（含原因） |
| `DrainPlan` | struct | 排出计划（基线行数 + 追赶行数） |

## 依赖关系

- **引用的 crate**: 无外部依赖
- **被引用方**: `streaming::commit_tick`

## 实现备注

- 双档系统：Smooth（稳定基线显示节奏）和 CatchUp（积压存在时全量排空）
- 使用滞后（hysteresis）逻辑避免在阈值边界快速切换
- 进入追赶模式的阈值高于退出阈值
- 退出后有 `REENTER_CATCH_UP_HOLD` 抑制立即重入
