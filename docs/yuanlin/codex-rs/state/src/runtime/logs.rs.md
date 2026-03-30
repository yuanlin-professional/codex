# `logs.rs` — 日志 SQLite 持久化操作

## 功能概述
该文件为 `StateRuntime` 实现日志的批量插入、分区裁剪、查询和反馈日志导出功能。日志按线程 ID 和进程 UUID 分区管理，每个分区维持 10 MiB / 1000 行的保留上限。支持全功能的日志查询（级别、时间、模块、线程、全文搜索）和面向反馈的日志导出（合并线程日志和同进程无线程日志）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `FeedbackLogRow` | 结构体(内部) | 反馈日志行：ts、ts_nanos、level、feedback_log_body |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `insert_logs` | `async (&self, entries) -> Result<()>` | 批量插入日志并在同一事务中裁剪超限分区 |
| `prune_logs_after_insert` | `async (&self, entries, tx) -> Result<()>` | 使用窗口函数裁剪超限的线程/进程分区 |
| `query_logs` | `async (&self, query) -> Result<Vec<LogRow>>` | 带可选过滤器的日志查询 |
| `query_feedback_logs` | `async (&self, thread_id) -> Result<Vec<u8>>` | 查询格式化的反馈日志（含同进程无线程日志） |
| `query_feedback_logs_for_threads` | `async (&self, thread_ids) -> Result<Vec<u8>>` | 多线程反馈日志查询 |

## 依赖关系
- **引用的 crate**: `sqlx`, `chrono`
- **被引用方**: `log_db.rs`, `codex-core`

## 实现备注
- 裁剪使用 SQL 窗口函数（cumulative SUM + ROW_NUMBER）高效删除最旧超限行
- 反馈日志查询通过 CTE 合并线程日志和最近进程的无线程日志
- 包含大量集成测试覆盖各种裁剪边界情况
