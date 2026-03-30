# exec-server/src/server — 服务器模块

## 功能概述
exec-server 的服务器端实现，包括处理器、传输层和注册表。

## 目录结构
| 文件 | 说明 |
|------|------|
| `file_system_handler.rs` | 文件系统请求处理器 |
| `handler.rs` | 请求处理器入口 |
| `jsonrpc.rs` | JSON-RPC 协议 |
| `process_handler.rs` | 进程请求处理器 |
| `processor.rs` | 消息处理器 |
| `registry.rs` | 服务注册表 |
| `transport_tests.rs` | 传输层测试 |
| `transport.rs` | 传输层实现 |
