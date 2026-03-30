# `thread_metadata.rs` — 线程元数据数据模型

## 功能概述
该文件定义了 Codex 线程系统的核心数据模型。`ThreadMetadata` 包含线程的完整元数据（ID、rollout 路径、时间戳、来源、模型、Git 信息等），`ThreadMetadataBuilder` 提供从配置构建初始元数据的能力。还包括排序键、分页锚点、页面和回填统计等辅助类型。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ThreadMetadata` | 结构体 | 完整线程元数据（22 个字段） |
| `ThreadMetadataBuilder` | 结构体 | 线程元数据构建器 |
| `SortKey` | 枚举 | 排序键：CreatedAt/UpdatedAt |
| `Anchor` | 结构体 | 分页锚点：时间戳 + UUID |
| `ThreadsPage` | 结构体 | 线程分页结果 |
| `ExtractionOutcome` | 结构体 | 元数据提取结果 |
| `BackfillStats` | 结构体 | 回填统计 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ThreadMetadataBuilder::build` | `(&self, default_provider) -> ThreadMetadata` | 从构建器构造元数据 |
| `ThreadMetadata::prefer_existing_git_info` | `(&mut self, existing)` | 保留已有非空 Git 字段 |
| `ThreadMetadata::diff_fields` | `(&self, other) -> Vec<&str>` | 比较两个元数据的差异字段 |

## 依赖关系
- **引用的 crate**: `chrono`, `codex_protocol`, `sqlx`, `uuid`
- **被引用方**: 整个 `codex-state` crate

## 实现备注
- 时间戳规范化截断纳秒部分
- `ThreadRow` 是 SQLite 行的内部表示，通过 `TryFrom` 转换为 `ThreadMetadata`
