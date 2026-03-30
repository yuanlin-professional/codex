# `message_history.rs` — 全局消息历史持久化层

## 功能概述

该文件实现了 Codex 的全局消息历史持久化功能，历史记录存储在 `~/.codex/history.jsonl` 文件中，采用 JSON Lines 格式（每行一个 JSON 对象）。通过文件锁（advisory file locking）保证多进程并发写入的安全性，支持历史大小上限管理（超出时从最旧记录开始裁剪），提供基于文件标识（inode/创建时间）和行偏移的精确查找功能。在 Unix 系统上强制设置文件权限为 `0o600` 保护隐私。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `HistoryEntry` | struct | 历史条目：session_id、时间戳（Unix 秒）、文本内容 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `append_entry` | `async fn(text, conversation_id, config) -> Result<()>` | 追加一条历史记录，使用文件锁保证原子写入 |
| `history_metadata` | `async fn(config) -> (u64, usize)` | 异步获取历史文件的标识 ID 和条目数 |
| `lookup` | `fn(log_id, offset, config) -> Option<HistoryEntry>` | 同步查找指定偏移位置的历史条目 |
| `enforce_history_limit` | `fn(file, max_bytes) -> Result<()>` | 在持有写锁时裁剪历史文件到软上限 |
| `ensure_owner_only_permissions` | `async fn(file) -> Result<()>` | Unix 上确保文件权限为 0o600 |
| `history_log_id` | `fn(metadata) -> Option<u64>` | 获取文件标识（Unix: inode, Windows: creation_time） |
| `trim_target_bytes` | `fn(max_bytes, newest_entry_len) -> u64` | 计算裁剪目标大小（软上限为硬上限的 80%） |

## 依赖关系

- **引用的 crate**: `serde`/`serde_json`（序列化）、`tokio`（异步文件和任务）、`codex_protocol`（ThreadId）
- **被引用方**: TUI 层的历史浏览功能、会话层的历史记录

## 实现备注

- 写入使用排他文件锁（`try_lock`）+ 重试循环（最多 10 次，每次间隔 100ms），在 `spawn_blocking` 中执行避免阻塞异步运行时。
- Unix 上使用 `O_APPEND` 标志打开文件，配合 POSIX 的原子追加写保证（小于 PIPE_BUF 的写操作是原子的）。
- 历史大小管理采用"硬上限触发，裁剪到软上限"策略：软上限为硬上限的 80%（`HISTORY_SOFT_CAP_RATIO = 0.8`），避免频繁裁剪。裁剪时始终保留最新条目。
- 查找操作使用共享文件锁（`try_lock_shared`），允许多个读者并发访问。
- 通过文件标识（`log_id`）验证历史文件未被替换或截断，保证查找结果的一致性。
- `HistoryEntry` 的 `session_id` 字段（对应 `conversation_id`）的名称保持向后兼容。
- 当配置 `HistoryPersistence::None` 时，`append_entry` 直接返回而不执行任何操作。
