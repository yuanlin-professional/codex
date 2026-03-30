# `lib.rs` — codex-state crate 入口

## 功能概述
该文件是 `codex-state` crate 的根模块，提供 SQLite 持久化状态管理。声明并重导出所有子模块的公共类型，包括 `StateRuntime`、线程元数据模型、Agent Job 模型、日志模型、Backfill 状态、记忆系统（Stage1Output 等）。定义了数据库文件名常量、版本号和指标名称常量。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 重导出类型 | — | `StateRuntime`, `ThreadMetadata`, `AgentJob`, `LogEntry` 等 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| 重导出函数 | — | `state_db_path`, `logs_db_path`, `apply_rollout_item` 等 |

## 依赖关系
- **引用的 crate**: 无直接外部依赖（仅重导出内部模块）
- **被引用方**: `codex-rollout`, `codex-core`

## 实现备注
- STATE_DB_VERSION = 5, LOGS_DB_VERSION = 1
- 提供 `SQLITE_HOME_ENV` 环境变量用于覆盖数据库目录
