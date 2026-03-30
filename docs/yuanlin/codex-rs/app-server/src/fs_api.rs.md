# `fs_api.rs` — 文件系统操作 API

## 功能概述

本模块实现了 `FsApi` 结构体，为 app-server 提供文件系统操作的 JSON-RPC 接口封装。支持的操作包括：读取文件（返回 base64 编码）、写入文件（接受 base64 编码数据）、创建目录、获取文件/目录元数据、读取目录内容列表、删除文件/目录、以及文件/目录复制。所有操作通过 `codex_exec_server::ExecutorFileSystem` trait 执行，支持不同运行环境。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `FsApi` | 结构体 | 文件系统 API，持有 `Arc<dyn ExecutorFileSystem>` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `read_file` | `async fn(&self, params) -> Result<FsReadFileResponse, ...>` | 读取文件并返回 base64 内容 |
| `write_file` | `async fn(&self, params) -> Result<FsWriteFileResponse, ...>` | 写入 base64 解码后的文件数据 |
| `create_directory` | `async fn(&self, params) -> Result<...>` | 创建目录（默认递归） |
| `get_metadata` | `async fn(&self, params) -> Result<...>` | 获取文件/目录元数据 |
| `read_directory` | `async fn(&self, params) -> Result<...>` | 列出目录内容 |
| `remove` | `async fn(&self, params) -> Result<...>` | 删除文件/目录 |
| `copy` | `async fn(&self, params) -> Result<...>` | 复制文件/目录 |
| `invalid_request` | `fn(message) -> JSONRPCErrorError` | 构造无效请求错误 |
| `map_fs_error` | `fn(err) -> JSONRPCErrorError` | 将 I/O 错误映射为 JSON-RPC 错误 |

## 依赖关系

- **引用的 crate**: `codex_exec_server`（`ExecutorFileSystem`、`Environment`）、`codex_app_server_protocol`、`base64`
- **被引用方**: `message_processor.rs`（处理 `fs/*` 请求时调用）

## 实现备注

- `InvalidInput` 类型的 I/O 错误映射为 `INVALID_REQUEST_ERROR_CODE`，其他映射为 `INTERNAL_ERROR_CODE`。
