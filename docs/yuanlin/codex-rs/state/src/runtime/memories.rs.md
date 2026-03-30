# `memories.rs` — 记忆系统 SQLite 持久化操作

## 功能概述
该文件为 `StateRuntime` 实现记忆（Memory）系统的完整 SQLite 操作。包括 Stage-1 提取作业的认领与完成、Stage-1 输出的存储与查询、Phase-2 合并作业管理、记忆数据清理和使用记录等。支持基于租约的并发控制和重试退避机制。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无新增类型 | — | 类型定义在 `model/memories.rs` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `clear_memory_data` | `async (&self) -> Result<()>` | 清除所有记忆状态 |
| `reset_memory_data_for_fresh_start` | `async (&self) -> Result<()>` | 重置记忆并禁用现有线程的记忆生成 |
| `record_stage1_output_usage` | `async (&self, thread_ids) -> Result<usize>` | 记录 Stage-1 输出的使用计数 |
| `try_claim_stage1_job` | 未完全展示 | 尝试认领 Stage-1 提取作业 |

## 依赖关系
- **引用的 crate**: `sqlx`, `chrono`, `uuid`
- **被引用方**: `codex-core` 记忆生成流程

## 实现备注
- 使用 ownership_token（UUID）实现租约式并发控制
- 默认重试次数为 3
