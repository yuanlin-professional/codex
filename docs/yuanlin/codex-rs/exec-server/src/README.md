# exec-server/src — 源代码目录

## 功能概述
exec-server crate 的源代码实现目录。提供远程命令执行服务器。

## 目录结构
| 文件 | 说明 |
|------|------|
| `client_api.rs` | 客户端 API |
| `client.rs` | 客户端实现 |
| `connection.rs` | 连接管理 |
| `environment.rs` | 环境变量处理 |
| `file_system.rs` | 文件系统接口 |
| `lib.rs` | 库入口 |
| `local_file_system.rs` | 本地文件系统 |
| `local_process.rs` | 本地进程 |
| `process_id.rs` | 进程 ID |
| `process.rs` | 进程抽象 |
| `protocol.rs` | 通信协议 |
| `remote_file_system.rs` | 远程文件系统 |
| `remote_process.rs` | 远程进程 |
| `rpc.rs` | RPC 通信 |
| `server.rs` | 服务器入口 |
