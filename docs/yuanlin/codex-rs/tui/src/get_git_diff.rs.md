# `get_git_diff.rs` — Git 差异获取工具

## 功能概述

本文件提供获取当前工作目录 Git 差异的异步工具函数。它同时获取已跟踪文件的差异和未跟踪文件的差异（通过 `git diff --no-index` 对比 /dev/null），并将结果拼接返回。当目录不在 Git 仓库中时返回空差异。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `get_git_diff` | `async fn() -> io::Result<(bool, String)>` | 返回 (是否在 Git 仓库中, 差异文本) |

## 依赖关系

- **引用的 crate**: `tokio::process`（异步 Git 命令执行）
- **被引用方**: `app.rs`（`/diff` 命令处理）

## 实现备注

- 已跟踪差异和未跟踪文件列表并行获取（`tokio::join!`）。
- 未跟踪文件的差异通过 `JoinSet` 并行处理。
- Git 返回码 1 被视为正常（表示存在差异）。
- 跨平台支持：Windows 使用 `NUL`，其他平台使用 `/dev/null`。
