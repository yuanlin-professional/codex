# `storage.rs` -- 记忆文件系统存储管理

## 功能概述

管理记忆子系统在文件系统上的产物存储，包括 `raw_memories.md` 文件的重建和 `rollout_summaries/` 目录下摘要文件的同步。负责将数据库中的 stage-1 输出同步到磁盘文件，支持增量更新和过期清理。rollout 摘要文件名采用时间戳-短哈希-slug 的格式，确保全局唯一且人类可读。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| （无自定义结构体） | — | 该文件以函数为主 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `rebuild_raw_memories_file_from_memories` | `async fn(&Path, &[Stage1Output], usize) -> io::Result<()>` | 从 DB 数据重建 raw_memories.md 文件 |
| `sync_rollout_summaries_from_memories` | `async fn(&Path, &[Stage1Output], usize) -> io::Result<()>` | 同步 rollout 摘要文件，清理过期文件，无记忆时移除索引文件 |
| `rollout_summary_file_stem` | `fn(&Stage1Output) -> String` | 根据 stage-1 输出生成摘要文件名前缀 |
| `rollout_summary_file_stem_from_parts` | `fn(ThreadId, DateTime, Option<&str>) -> String` | 从组件生成格式为 `YYYY-MM-DDThh-mm-ss-HASH[-slug]` 的文件名 |
| `prune_rollout_summaries` | `async fn(&Path, &HashSet<String>) -> io::Result<()>` | 清理不在保留集合中的摘要文件 |
| `write_rollout_summary_for_thread` | `async fn(&Path, &Stage1Output) -> io::Result<()>` | 为单个线程写入摘要文件 |
| `retained_memories` | `fn(&[Stage1Output], usize) -> &[Stage1Output]` | 截取前 N 条记忆作为保留集 |

## 依赖关系

- **引用的 crate**: `codex_state`、`uuid`、`chrono`、`tokio::fs`
- **被引用方**: `phase2.rs` 调用 `sync_rollout_summaries_from_memories` 和 `rebuild_raw_memories_file_from_memories`；`prompts.rs` 调用 `rollout_summary_file_stem_from_parts`

## 实现备注

- 文件名中的短哈希使用 4 位 base-62 编码（字母表空间 14,776,336），从 UUID 低 32 位或字符串哈希生成。
- slug 被截断到最大 60 字符，非字母数字字符替换为下划线，末尾下划线被移除。
- 当保留的记忆为空时，会主动清理 `MEMORY.md`、`memory_summary.md` 和 `skills/` 目录。
- `raw_memories.md` 按"最新优先"排序，每条记忆包含线程 ID、时间戳、工作目录、rollout 路径和摘要文件引用。
