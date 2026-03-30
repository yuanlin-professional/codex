# `personality_migration.rs` — 个性设置自动迁移

## 功能概述

此文件实现了 Codex 个性（personality）设置的一次性自动迁移逻辑。当用户从旧版本升级后尚无显式个性设置时，如果已有历史会话记录，则自动将个性设置迁移为 `Pragmatic`（务实模式），以保持行为一致性。迁移通过标记文件（`.personality_migration`）确保只执行一次。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PersonalityMigrationStatus` | 枚举 | 迁移状态：`SkippedMarker`（已迁移）、`SkippedExplicitPersonality`（已有显式设置）、`SkippedNoSessions`（无历史会话）、`Applied`（已应用） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `maybe_migrate_personality` | `async (codex_home, config_toml) -> io::Result<PersonalityMigrationStatus>` | 条件性执行个性迁移 |
| `has_recorded_sessions` | `async (codex_home, default_provider) -> io::Result<bool>` | 检查是否存在历史会话记录 |
| `create_marker` | `async (marker_path) -> io::Result<()>` | 创建迁移标记文件 |

## 依赖关系

- **引用的 crate**: `crate::config`（`ConfigToml`、`ConfigEditsBuilder`）、`crate::rollout`、`crate::state_db`、`codex_protocol`
- **被引用方**: `lib.rs` 公开导出此模块，应用启动时调用

## 实现备注

- 迁移标记文件使用 `create_new(true)` 创建以避免竞态条件
- 历史会话检查依次查询 state_db、sessions 目录和 archived_sessions 目录
- 只需找到一条会话即认为有历史，使用 `page_size = 1` 优化查询
- 如果 config_toml 中已有 `personality` 设置（全局或配置文件级别），直接跳过迁移
- 迁移使用 `ConfigEditsBuilder` 持久化到配置文件中
