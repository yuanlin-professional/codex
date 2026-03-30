# `codex_message_processor.rs` — Codex 核心消息处理器（线程/Turn/MCP/插件管理）

## 功能概述

本模块是 app-server 中最大的源文件（约 9300 行），实现了 `CodexMessageProcessor` 结构体，作为 `MessageProcessor` 的核心委托组件。它负责处理所有业务层面的客户端请求，包括：线程（thread）的创建/恢复/删除/列表、Turn 的启动/中断/回滚、模型列表、认证管理（登录/登出/API key）、配置读写、MCP 服务器状态管理与 OAuth 登录、插件安装/卸载/列表、Apps 列表、文件搜索、命令执行、文件系统操作、反馈上传、协作模式、外部代理配置迁移等。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CodexMessageProcessor` | 结构体 | 核心业务处理器，持有 `ThreadManager`、`ConfigApi`、`CommandExecManager`、认证管理器等 |
| `CodexMessageProcessorArgs` | 结构体 | 构造 `CodexMessageProcessor` 所需的参数包 |
| `ApiVersion` | 枚举 | 区分 v1（JSON-RPC）和 v2（类型化）API 版本 |
| `ThreadListenerHandle` | 结构体 | 线程事件监听器的句柄，包含取消 token 和 generation 计数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle_client_request` | `async fn(&mut self, request, connection_id, session)` | v2 类型化客户端请求的分发入口 |
| `handle_thread_start` | `async fn(...)` | 创建新线程并启动事件监听 |
| `handle_turn_start` | `async fn(...)` | 在已有线程上启动新的对话 Turn |
| `handle_turn_interrupt` | `async fn(...)` | 中断正在运行的 Turn |
| `try_attach_thread_listener` | `async fn(thread_id, connection_ids)` | 为已创建的线程附加事件监听器 |
| `drain_background_tasks` | `async fn(&self)` | 等待所有后台任务完成 |
| `shutdown_threads` | `async fn(&self)` | 关闭所有活跃线程 |

## 依赖关系

- **引用的 crate**: `codex_core`（`ThreadManager`、`CodexThread`、`Config`）、`codex_app_server_protocol`（所有请求/响应类型）、`codex_chatgpt`（connectors）、`codex_login`（认证）
- **被引用方**: `message_processor.rs`（`MessageProcessor` 持有并委托给此处理器）

## 实现备注

- 线程事件监听使用 `tokio::spawn` 创建后台任务，通过 `oneshot` channel 支持取消。
- 每个线程有独立的 `ThreadState`（包含中断队列、回滚状态、Turn 摘要等）。
- Apps 列表加载通过异步任务并行获取全量和可访问两类 connector 列表。
- MCP OAuth 登录在后台任务中执行静默登录，完成后发送通知。
