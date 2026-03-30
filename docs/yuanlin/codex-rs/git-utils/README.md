# git-utils

## 功能概述

`codex-git-utils` 是 Codex 项目的 Git 工具集模块，提供全面的 Git 仓库操作和信息查询能力。核心功能包括：仓库信息收集、分支管理、补丁应用、"幽灵提交"（Ghost Commit）快照与恢复、符号链接创建等。它是多个上层模块（如 `codex-core`、`codex-analytics`、`codex-secrets`、`codex-cloud-tasks`）的基础依赖。

## 架构说明

该 crate 按功能领域组织为以下子模块：

### 1. info - 仓库信息查询
提供丰富的 Git 仓库元数据查询：
- 仓库根目录、HEAD 提交哈希、当前分支名、默认分支名
- 远程仓库 URL、本地分支列表
- 最近提交记录、与远程的差异
- 变更检测

### 2. apply - 补丁应用
Git 补丁的应用、暂存和输出解析工具。

### 3. branch - 分支操作
提供 `merge_base_with_head` 等分支比较功能。

### 4. ghost_commits - 幽灵提交
核心特性 -- "幽灵提交"机制，用于在不影响正常 Git 历史的情况下创建仓库状态快照：
- `create_ghost_commit` - 将当前工作目录状态（含未跟踪文件）打包为一个独立提交
- `restore_ghost_commit` - 从幽灵提交恢复仓库状态
- `capture_ghost_snapshot_report` - 快照报告（含被忽略的未跟踪文件和大目录信息）

### 5. platform - 跨平台操作
提供 `create_symlink` 等跨平台文件系统操作。

### 6. errors - 错误类型
`GitToolingError` 统一错误类型。

## 目录结构

```
git-utils/
  Cargo.toml
  README.md           # 已有的英文文档
  src/
    lib.rs            # 模块入口，统一导出
    info.rs           # 仓库信息查询
    apply.rs          # 补丁应用
    branch.rs         # 分支操作
    ghost_commits.rs  # 幽灵提交机制
    operations.rs     # Git 命令操作封装
    platform.rs       # 跨平台工具
    errors.rs         # 错误类型
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-utils-absolute-path` | 绝对路径工具 |
| `futures` | 异步工具 |
| `once_cell` | 延迟初始化 |
| `regex` | 正则表达式（Git 输出解析） |
| `schemars` | JSON Schema 生成 |
| `serde` | 序列化 |
| `tempfile` | 临时文件 |
| `thiserror` | 错误类型派生 |
| `tokio` | 异步进程执行（`process`、`time`） |
| `ts-rs` | TypeScript 类型生成 |
| `walkdir` | 目录遍历 |

## 核心接口/API

### 仓库信息查询

```rust
pub fn get_git_repo_root(cwd: &Path) -> Option<PathBuf>;
pub fn get_head_commit_hash(repo_root: &Path) -> Result<String>;
pub fn current_branch_name(repo_root: &Path) -> Result<String>;
pub fn default_branch_name(repo_root: &Path) -> Result<String>;
pub fn get_git_remote_urls(cwd: &Path) -> Result<Vec<String>>;
pub fn get_has_changes(repo_root: &Path) -> Result<bool>;
pub fn local_git_branches(repo_root: &Path) -> Result<Vec<String>>;
pub fn recent_commits(repo_root: &Path) -> Result<Vec<CommitLogEntry>>;
pub async fn collect_git_info(cwd: &Path) -> Result<GitInfo>;
pub async fn git_diff_to_remote(repo_root: &Path) -> Result<GitDiffToRemote>;
```

### 幽灵提交

```rust
pub async fn create_ghost_commit(options: CreateGhostCommitOptions) -> Result<GhostCommit>;
pub async fn restore_ghost_commit(options: RestoreGhostCommitOptions) -> Result<()>;
pub async fn capture_ghost_snapshot_report(config: &GhostSnapshotConfig) -> Result<GhostSnapshotReport>;
```

### 补丁应用

```rust
pub async fn apply_git_patch(request: ApplyGitRequest) -> Result<ApplyGitResult>;
pub fn extract_paths_from_patch(patch: &str) -> Vec<PathBuf>;
pub fn stage_paths(repo_root: &Path, paths: &[PathBuf]) -> Result<()>;
```

### 类型定义

- **`GitSha`** - Git SHA 封装
- **`GhostCommit`** - 幽灵提交信息（id、parent、未跟踪文件/目录列表）
- **`GitInfo`** - 仓库信息聚合
- **`CommitLogEntry`** - 提交日志条目
- **`GitDiffToRemote`** - 与远程分支的差异信息
- **`GitToolingError`** - Git 工具错误类型
