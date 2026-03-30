# `client.rs` — exec-server 客户端实现

## 功能概述

该文件实现了 `ExecServerClient`，负责通过 WebSocket 连接远程 exec-server，完成 JSON-RPC 握手（initialize/initialized），并提供进程管理（exec、read、write、terminate）和文件系统操作（readFile、writeFile、createDirectory、getMetadata、readDirectory、remove、copy）的 RPC 调用方法。内部维护一个基于 `ArcSwap` 的 process_id -> session 注册表，将服务端通知路由到对应的进程会话。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecServerClient` | struct | exec-server 客户端核心句柄，封装 RPC 客户端和会话注册表 |
| `ExecServerError` | enum | 连接/协议/JSON/服务端错误的统一错误类型 |
| `Session` | struct | 单个远程进程的会话，支持 read/write/terminate |
| `SessionState` | struct | 会话内部状态，含 watch channel 和故障信息 |
| `Inner` | struct | 客户端内部状态：RPC 客户端、会话表、写锁、reader 任务 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `connect_websocket` | `async fn(RemoteExecServerConnectArgs) -> Result<Self>` | WebSocket 连接并完成初始化握手 |
| `initialize` | `async fn(&self, options) -> Result<InitializeResponse>` | 发送 initialize 请求并等待响应 |
| `exec` | `async fn(&self, ExecParams) -> Result<ExecResponse>` | 启动远程进程 |
| `read` | `async fn(&self, ReadParams) -> Result<ReadResponse>` | 读取进程输出 |
| `register_session` | `async fn(&self, &ProcessId) -> Result<Session>` | 注册进程会话到路由表 |
| `handle_server_notification` | `async fn(inner, notification) -> Result<()>` | 处理服务端 output/exited/closed 通知 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`, `arc_swap`, `tokio`, `tokio_tungstenite`, `serde_json`, `thiserror`, `tracing`
- **被引用方**: `environment.rs`, `remote_process.rs`, `remote_file_system.rs`, `lib.rs`

## 实现备注

- 使用 `ArcSwap` 实现读多写少的并发会话表，写入时通过 `Mutex` 保证原子性。
- reader 任务使用 `Arc::new_cyclic` 避免循环引用导致内存泄漏。
- 当传输断开时，通过 `fail_all_sessions` 合成错误响应给所有活跃会话。
- 包含单元测试验证唤醒通知不会阻塞其他会话。
