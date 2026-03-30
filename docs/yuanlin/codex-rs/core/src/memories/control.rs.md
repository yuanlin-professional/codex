# `control.rs` -- 记忆根目录内容清理

## 功能概述

提供异步函数 `clear_memory_root_contents`，用于安全地清空记忆根目录下的所有内容（文件和子目录），同时保留根目录本身。该函数会拒绝对符号链接指向的目录执行清理操作，以防止误删外部文件。如果根目录不存在则会自动创建。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `clear_memory_root_contents` | `async fn(&Path) -> io::Result<()>` | 清空记忆根目录内容，拒绝符号链接目录，不存在时自动创建 |

## 依赖关系

- **引用的 crate**: `std::path::Path`、`tokio::fs`
- **被引用方**: `memories/mod.rs` 通过 `pub(crate) use` 导出供外部使用

## 实现备注

- 使用 `tokio::fs::symlink_metadata` 检测符号链接，避免 follow symlink 带来的安全风险。
- 对目录使用 `remove_dir_all`，对文件使用 `remove_file`，逐项删除以确保清理彻底。
