# `turn_metadata.rs` — 回合元数据收集与 HTTP 头构建

## 功能概述

此文件负责构建和管理每个 API 回合（turn）的元数据，包括会话 ID、回合 ID、沙箱标签和 Git 工作区信息（远程 URL、最新提交哈希、是否有未提交变更）。元数据被序列化为 JSON 字符串作为 HTTP 请求头发送给模型 API。Git 元数据的获取通过后台异步任务完成，以避免阻塞主流程。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TurnMetadataBag` | 结构体 | 回合元数据包，包含 session_id、turn_id、workspaces 和 sandbox |
| `TurnMetadataState` | 结构体 | 回合元数据状态管理，支持基础头和异步 Git 丰富头 |
| `WorkspaceGitMetadata` | 结构体（私有） | Git 工作区元数据：远程 URL、提交哈希、变更状态 |
| `TurnMetadataWorkspace` | 结构体（私有） | 可序列化的工作区元数据 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_turn_metadata_header` | `async (cwd, sandbox) -> Option<String>` | 构建无 session/turn ID 的元数据头（公开 API） |
| `TurnMetadataState::new` | `(session_id, turn_id, cwd, sandbox_policy, windows_sandbox_level) -> Self` | 创建新的回合元数据状态，初始只含基础信息 |
| `TurnMetadataState::current_header_value` | `(&self) -> Option<String>` | 返回当前可用的最佳头值（优先使用丰富版） |
| `TurnMetadataState::spawn_git_enrichment_task` | `(&self)` | 启动后台任务获取 Git 工作区元数据 |
| `TurnMetadataState::cancel_git_enrichment_task` | `(&self)` | 取消正在运行的 Git 丰富任务 |

## 依赖关系

- **引用的 crate**: `codex_git_utils`（Git 操作）、`codex_protocol`、`serde`/`serde_json`、`tokio`
- **被引用方**: `lib.rs` 导出 `build_turn_metadata_header`，回合执行流程使用 `TurnMetadataState`

## 实现备注

- 采用两阶段策略：先立即构建基础头（仅含 session/turn ID 和 sandbox），再异步丰富 Git 信息
- `enriched_header` 使用 `Arc<RwLock>` 保护，支持并发安全的读写
- Git 丰富任务使用 `tokio::spawn` 独立运行，可通过 `abort()` 取消
- `PoisonError::into_inner` 用于处理锁中毒，确保即使 panic 也能继续运行
- 当不在 Git 仓库中时跳过丰富任务的启动
