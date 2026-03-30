# `ghost_commits.rs` — Ghost Commit 快照与恢复

## 功能概述
实现 Git 工作区的"幽灵提交"快照机制：在不影响用户分支历史的前提下，使用临时索引创建包含完整工作区状态（已跟踪修改、未跟踪文件、甚至 .gitignore 中的文件）的独立 commit 对象。支持快照恢复（undo），正确处理大文件跳过、大目录忽略和默认忽略目录（node_modules, .venv 等）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `CreateGhostCommitOptions` | 结构体 | 创建选项：repo_path, message, force_include, 快照配置 |
| `RestoreGhostCommitOptions` | 结构体 | 恢复选项：repo_path, 快照配置 |
| `GhostSnapshotConfig` | 结构体 | 快照配置：大文件/大目录阈值、是否禁用警告 |
| `GhostSnapshotReport` | 结构体 | 快照报告：被忽略的大目录和大文件列表 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_ghost_commit` | `fn(options) -> Result<GhostCommit>` | 创建幽灵提交快照 |
| `create_ghost_commit_with_report` | `fn(options) -> Result<(GhostCommit, Report)>` | 创建快照并返回报告 |
| `restore_ghost_commit` | `fn(repo_path, commit) -> Result<()>` | 恢复工作区到快照状态 |

## 依赖关系
- **引用的 crate**: `tempfile`, `crate::operations`
- **被引用方**: codex-core 的 undo 功能

## 实现备注
包含 18 个测试用例，覆盖端到端往返、大文件/大目录跳过、默认忽略目录、子目录恢复、.gitignore 文件保留等场景。
