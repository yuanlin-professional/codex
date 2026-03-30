# `agent_jobs.rs` — Agent Job SQLite 持久化操作

## 功能概述
该文件为 `StateRuntime` 实现 Agent Job（代理批处理作业）的完整 CRUD 操作。包括创建作业及其条目、状态流转（pending/running/completed/failed/cancelled）、条目结果报告和进度统计等。所有操作通过 SQLx 直接执行 SQL。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无新增类型 | — | 所有类型定义在 `model/agent_job.rs` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_agent_job` | `async (&self, params, items) -> Result<AgentJob>` | 在事务中创建作业和所有条目 |
| `get_agent_job` | `async (&self, job_id) -> Result<Option<AgentJob>>` | 按 ID 查询作业 |
| `mark_agent_job_running` | `async (&self, job_id) -> Result<()>` | 标记作业为运行中 |
| `report_agent_job_item_result` | `async (&self, job_id, item_id, thread_id, result) -> Result<bool>` | 原子性报告条目结果（仅接受正确线程的报告） |
| `get_agent_job_progress` | `async (&self, job_id) -> Result<AgentJobProgress>` | 获取作业进度（各状态条目数） |
| `mark_agent_job_cancelled` | `async (&self, job_id, reason) -> Result<bool>` | 取消作业（仅限 pending/running 状态） |

## 依赖关系
- **引用的 crate**: `sqlx`, `chrono`, `serde_json`
- **被引用方**: `codex-core` Agent Job 流程

## 实现备注
- 条目结果报告使用 assigned_thread_id 做乐观锁，防止迟到报告覆盖
- 创建作业使用事务保证原子性
