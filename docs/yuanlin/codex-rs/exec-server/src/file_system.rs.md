# `file_system.rs` — 文件系统抽象 trait

## 功能概述

定义 `ExecutorFileSystem` trait 及相关选项结构体（`CreateDirectoryOptions`、`RemoveOptions`、`CopyOptions`）和元数据类型（`FileMetadata`、`ReadDirectoryEntry`），为本地和远程文件系统操作提供统一的异步接口。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecutorFileSystem` | trait | 文件系统异步操作 trait（read/write/mkdir/metadata/readdir/remove/copy） |
| `CreateDirectoryOptions` | struct | 创建目录的选项（是否递归） |
| `RemoveOptions` | struct | 删除的选项（递归、强制） |
| `CopyOptions` | struct | 复制的选项（是否递归） |
| `FileMetadata` | struct | 文件元信息（类型、创建/修改时间） |
| `ReadDirectoryEntry` | struct | 目录条目（名称、类型） |

## 依赖关系

- **引用的 crate**: `async_trait`, `codex_utils_absolute_path`, `tokio::io`
- **被引用方**: `local_file_system.rs`, `remote_file_system.rs`, `environment.rs`
