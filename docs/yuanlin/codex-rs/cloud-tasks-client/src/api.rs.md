# `api.rs` — 云任务 API 类型与 trait 定义

## 功能概述

定义云任务系统的核心数据类型和 `CloudBackend` trait。包含任务状态（Pending/Ready/Applied/Error）、任务摘要、diff 摘要、申请结果、尝试状态等类型。`CloudBackend` trait 定义了任务的 CRUD 操作：列表、获取摘要/diff/消息、列出兄弟尝试、预检/应用任务、创建任务。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CloudTaskError` | enum | 错误类型：Unimplemented、Http、Io、Msg |
| `TaskId` | struct | 任务 ID 透明包装 |
| `TaskStatus` | enum | 任务状态：Pending、Ready、Applied、Error |
| `TaskSummary` | struct | 任务摘要：ID、标题、状态、更新时间、环境信息、diff 摘要 |
| `TurnAttempt` | struct | 轮次尝试：turn_id、状态、diff、消息 |
| `ApplyOutcome` | struct | 应用结果：是否应用、状态、跳过/冲突路径 |
| `CloudBackend` | trait | 云任务后端操作接口 |

## 依赖关系

- **引用的 crate**: `chrono`、`serde`、`async_trait`
- **被引用方**: TUI 云任务视图、http/mock 后端实现
