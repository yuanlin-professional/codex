# `backfill_state.rs` — 回填生命周期状态模型

## 功能概述
该文件定义了 rollout 元数据回填的生命周期状态模型。`BackfillState` 持有当前状态、最后水印和最后成功时间；`BackfillStatus` 枚举包含 Pending/Running/Complete 三种状态。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `BackfillState` | 结构体 | 回填状态：status + last_watermark + last_success_at |
| `BackfillStatus` | 枚举 | 回填生命周期：Pending/Running/Complete |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `BackfillState::try_from_row` | `(row) -> Result<Self>` | 从 SQLite 行解析 |
| `BackfillStatus::parse` | `(value) -> Result<Self>` | 从字符串解析状态 |

## 依赖关系
- **引用的 crate**: `chrono`, `sqlx`, `anyhow`
- **被引用方**: `runtime/backfill.rs`

## 实现备注
- 默认状态为 Pending
