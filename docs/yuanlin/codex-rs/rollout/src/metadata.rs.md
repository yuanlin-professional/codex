# `metadata.rs` — Rollout 元数据提取与回填

## 功能概述
该文件实现了从 rollout 文件中提取线程元数据的逻辑，以及基于 SQLite 的元数据回填（backfill）流程。包括从 session_meta 行构建 `ThreadMetadataBuilder`、批量回填 rollout 目录、租约认领回填工作者等功能。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无新增公开类型 | — | 使用 `codex_state` 的类型 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `builder_from_session_meta` | `(meta, rollout_path) -> Option<ThreadMetadataBuilder>` | 从 session_meta 构建线程元数据构建器 |

## 依赖关系
- **引用的 crate**: `codex_state`, `codex_protocol`, `chrono`
- **被引用方**: `recorder.rs`, `state_db.rs`

## 实现备注
- 回填批次大小为 200，租约时间 900 秒（测试中 1 秒）
