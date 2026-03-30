# `file_system_handler.rs` — 服务端文件系统请求处理器

## 功能概述

`FileSystemHandler` 将 JSON-RPC 文件系统请求（readFile、writeFile、createDirectory、getMetadata、readDirectory、remove、copy）委托给 `LocalFileSystem` 执行，处理 base64 编解码，并将 IO 错误映射为 JSON-RPC 错误响应。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `FileSystemHandler` | struct | 包装 LocalFileSystem 的请求处理器 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `read_file` | `async fn(&self, params) -> Result<FsReadFileResponse>` | 读取文件并返回 base64 |
| `write_file` | `async fn(&self, params) -> Result<FsWriteFileResponse>` | 解码 base64 并写入文件 |
| `map_fs_error` | `fn(io::Error) -> JSONRPCErrorError` | IO 错误到 JSON-RPC 错误的映射 |

## 依赖关系

- **引用的 crate**: `base64`, `codex_app_server_protocol`
- **被引用方**: `server/handler.rs`
