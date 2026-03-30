# `info.rs` — Git 仓库信息收集

## 功能概述
提供异步的 Git 仓库信息收集函数：commit hash、分支名、远程 URL、工作区变更状态、默认分支检测、最近提交日志、与远程 diff 的计算以及 worktree 根解析。所有 git 命令均带 5 秒超时防止大仓库阻塞。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `GitInfo` | 结构体 | Git 仓库信息：commit_hash, branch, repository_url |
| `GitDiffToRemote` | 结构体 | 与远程的 diff：sha 和 diff 文本 |
| `CommitLogEntry` | 结构体 | 提交日志条目：sha, timestamp, subject |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `collect_git_info` | `async fn(cwd) -> Option<GitInfo>` | 并行收集 Git 仓库信息 |
| `get_git_repo_root` | `fn(base_dir) -> Option<PathBuf>` | 查找 .git 目录确定仓库根 |
| `git_diff_to_remote` | `async fn(cwd) -> Option<GitDiffToRemote>` | 计算与最近远程 commit 的 diff |
| `default_branch_name` | `async fn(cwd) -> Option<String>` | 检测仓库默认分支名 |
| `recent_commits` | `async fn(cwd, limit) -> Vec<CommitLogEntry>` | 获取最近 N 条提交 |

## 依赖关系
- **引用的 crate**: `tokio`, `futures`, `codex_utils_absolute_path`
- **被引用方**: 分析模块、cloud-tasks、TUI
