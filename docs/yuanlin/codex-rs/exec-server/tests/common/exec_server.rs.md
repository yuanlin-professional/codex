# `exec_server.rs` — 集成测试辅助工具

## 功能概述

`ExecServerHarness` 是集成测试的辅助结构体，封装 exec-server 子进程的启动、WebSocket 连接、JSON-RPC 请求/通知发送以及事件接收。支持等待匹配条件的事件和超时控制。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecServerHarness` | struct | 测试用 exec-server 子进程管理器 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `exec_server` | `async fn() -> Result<ExecServerHarness>` | 启动 exec-server 并建立 WebSocket 连接 |
| `send_request` | `async fn(&mut self, method, params) -> Result<RequestId>` | 发送 JSON-RPC 请求 |
| `wait_for_event` | `async fn(&mut self, predicate) -> Result<JSONRPCMessage>` | 等待匹配事件 |

## 依赖关系

- **引用的 crate**: `codex_utils_cargo_bin`, `tokio_tungstenite`, `futures`
- **被引用方**: 所有 exec-server 集成测试

## 实现备注

- 从子进程 stdout 读取监听 URL。
- 连接失败时重试直到超时（5秒），处理进程启动延迟。
- Drop 时自动 kill 子进程。
