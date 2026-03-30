# `agent_job.rs` — Agent Job 数据模型定义

## 功能概述
该文件定义了 Agent Job（代理批处理作业）系统的完整数据模型，包括作业状态枚举、作业/条目元数据结构体、创建参数、进度统计以及 SQLite 行到领域模型的转换逻辑。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `AgentJobStatus` | 枚举 | 作业状态：Pending/Running/Completed/Failed/Cancelled |
| `AgentJobItemStatus` | 枚举 | 条目状态：Pending/Running/Completed/Failed |
| `AgentJob` | 结构体 | 作业元数据：ID、名称、指令、输入/输出路径、时间戳等 |
| `AgentJobItem` | 结构体 | 条目元数据：作业 ID、行索引、结果 JSON、线程分配等 |
| `AgentJobProgress` | 结构体 | 进度统计：各状态条目数 |
| `AgentJobCreateParams` | 结构体 | 作业创建参数 |
| `AgentJobItemCreateParams` | 结构体 | 条目创建参数 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `AgentJobStatus::parse` | `(value: &str) -> Result<Self>` | 从字符串解析作业状态 |
| `AgentJobStatus::is_final` | `(self) -> bool` | 判断是否为终态 |
| `TryFrom<AgentJobRow>` | `for AgentJob` | SQLite 行到领域模型的转换 |

## 依赖关系
- **引用的 crate**: `chrono`, `serde_json`, `sqlx`, `anyhow`
- **被引用方**: `runtime/agent_jobs.rs`

## 实现备注
- `AgentJobRow` 和 `AgentJobItemRow` 使用 `sqlx::FromRow` 派生
