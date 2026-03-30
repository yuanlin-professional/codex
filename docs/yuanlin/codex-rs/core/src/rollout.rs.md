# `rollout.rs` — 回滚存储桥接层

## 功能概述

此文件是 `codex_rollout` crate 的桥接层，将 `codex_core` 的 `Config` 类型适配为 `RolloutConfigView` trait，并重新导出回滚存储系统的所有公共类型和子模块（list、metadata、policy、recorder、session_index、truncation）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| （大量重导出） | — | `RolloutRecorder`、`SessionMeta`、`SESSIONS_SUBDIR`、`ARCHIVED_SESSIONS_SUBDIR` 等 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Config::codex_home` | `(&self) -> &Path` | 实现 `RolloutConfigView` |
| `Config::sqlite_home` | `(&self) -> &Path` | 实现 `RolloutConfigView` |
| `Config::cwd` | `(&self) -> &Path` | 实现 `RolloutConfigView` |
| `Config::model_provider_id` | `(&self) -> &str` | 实现 `RolloutConfigView` |
| `Config::generate_memories` | `(&self) -> bool` | 实现 `RolloutConfigView` |

## 依赖关系

- **引用的 crate**: `codex_rollout`（核心回滚库）、`crate::config::Config`
- **被引用方**: `lib.rs` 大量公开导出此模块的类型和函数

## 实现备注

- `RolloutConfigView` trait 实现将 `Config` 的字段映射为回滚系统所需的配置视图
- 子模块使用 `pub use` 和 `pub(crate) use` 分别控制公开和内部可见性
- `truncation` 子模块重导出 `thread_rollout_truncation` 的功能
- `metadata::builder_from_items` 是 `pub(crate)` 可见性
- 包含废弃函数 `find_conversation_path_by_id_str`（改用 `find_thread_path_by_id_str`）
