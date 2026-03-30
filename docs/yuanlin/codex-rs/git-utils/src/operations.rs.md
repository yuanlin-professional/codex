# `operations.rs` — Git 底层操作封装

## 功能概述
封装对系统 `git` 二进制的同步调用，提供仓库验证、HEAD 解析、仓库根解析、相对路径规范化、git 命令执行等基础操作。是 ghost_commits 和 branch 模块的底层依赖。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ensure_git_repository` | `fn(path) -> Result<()>` | 验证路径是否在 Git 工作区内 |
| `resolve_head` | `fn(path) -> Result<Option<String>>` | 解析 HEAD commit SHA |
| `resolve_repository_root` | `fn(path) -> Result<PathBuf>` | 解析 Git 仓库根目录 |
| `normalize_relative_path` | `fn(path) -> Result<PathBuf>` | 规范化相对路径，拒绝父目录逃逸 |
| `run_git_for_stdout` | `fn(dir, args, env) -> Result<String>` | 执行 git 命令并返回 trimmed stdout |
| `run_git_for_stdout_all` | `fn(dir, args, env) -> Result<String>` | 执行 git 并返回未 trim 的原始 stdout |

## 依赖关系
- **被引用方**: `ghost_commits.rs`, `branch.rs`
