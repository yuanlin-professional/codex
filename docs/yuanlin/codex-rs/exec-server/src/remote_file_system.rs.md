# `remote_file_system.rs` — 远程文件系统实现

## 功能概述

`RemoteFileSystem` 实现 `ExecutorFileSystem` trait，将文件系统操作通过 `ExecServerClient` 转发到远程 exec-server。数据传输使用 base64 编码。错误映射将 JSON-RPC 错误码 -32600 映射为 `InvalidInput`，其余映射为通用 IO 错误。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RemoteFileSystem` | struct | 远程文件系统客户端代理 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `read_file` | `async fn(&self, path) -> Result<Vec<u8>>` | 远程读取文件并解码 base64 |
| `write_file` | `async fn(&self, path, contents) -> Result<()>` | 编码 base64 后远程写入 |
| `map_remote_error` | `fn(ExecServerError) -> io::Error` | 错误类型映射 |

## 依赖关系

- **引用的 crate**: `base64`, `async_trait`, `tracing`
- **被引用方**: `environment.rs`
