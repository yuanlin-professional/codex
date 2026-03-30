# `local_file_system.rs` — 本地文件系统实现

## 功能概述

`LocalFileSystem` 实现 `ExecutorFileSystem` trait，通过 `tokio::fs` 提供本地文件系统的异步读写、目录创建/列举、元数据查询、删除和复制操作。复制操作支持递归目录复制和符号链接保持，并防止将目录复制到自身的子目录下。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `LocalFileSystem` | struct | 本地文件系统实现（无状态） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `read_file` | `async fn(&self, path) -> Result<Vec<u8>>` | 读取文件（限制 512MB） |
| `copy` | `async fn(&self, src, dst, options) -> Result<()>` | 支持文件/目录/符号链接的复制 |
| `copy_dir_recursive` | `fn(source, target) -> Result<()>` | 递归复制目录 |
| `copy_symlink` | `fn(source, target) -> Result<()>` | 跨平台符号链接复制 |

## 依赖关系

- **引用的 crate**: `tokio::fs`, `codex_utils_absolute_path`
- **被引用方**: `server/file_system_handler.rs`, `environment.rs`

## 实现备注

- 文件读取有 512MB 上限，防止内存溢出。
- 递归复制使用 `spawn_blocking` 在阻塞线程中执行同步 I/O。
- Windows 平台使用 `FileTypeExt` 判断 symlink 类型。
