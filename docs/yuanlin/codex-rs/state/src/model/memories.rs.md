# `memories.rs` — 记忆系统数据模型

## 功能概述
该文件定义了 Codex 记忆系统的数据模型，包括 Stage-1 提取输出（`Stage1Output`/`Stage1OutputRef`）、Phase-2 输入选择结果（`Phase2InputSelection`）、作业认领结果枚举（`Stage1JobClaimOutcome`/`Phase2JobClaimOutcome`）以及认领参数等。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Stage1Output` | 结构体 | Stage-1 提取输出：线程 ID、原始记忆、摘要、cwd、分支等 |
| `Stage1OutputRef` | 结构体 | Stage-1 输出的轻量引用（线程 ID + 更新时间） |
| `Phase2InputSelection` | 结构体 | Phase-2 输入选择：selected/previous/retained/removed |
| `Stage1JobClaimOutcome` | 枚举 | Stage-1 认领结果：Claimed/SkippedUpToDate/Running/RetryBackoff/Exhausted |
| `Phase2JobClaimOutcome` | 枚举 | Phase-2 认领结果：Claimed/NotDirty/Running |
| `Stage1JobClaim` | 结构体 | 已认领的 Stage-1 作业（含线程元数据和令牌） |
| `Stage1StartupClaimParams` | 结构体 | 启动时认领参数 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Stage1OutputRow::try_from_row` | `(row) -> Result<Self>` | 从 SQLite 行解析 |
| `stage1_output_ref_from_parts` | `(thread_id, ts, slug) -> Result<Stage1OutputRef>` | 从部件构造引用 |

## 依赖关系
- **引用的 crate**: `chrono`, `codex_protocol`, `sqlx`
- **被引用方**: `runtime/memories.rs`

## 实现备注
- Stage-1 认领支持 5 种结果状态用于细粒度并发控制
