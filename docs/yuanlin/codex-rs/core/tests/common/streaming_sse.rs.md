# `streaming_sse.rs` — 可控流式 SSE 测试服务器

## 功能概述

提供一个轻量级的手写 HTTP 服务器（`StreamingSseServer`），专门用于需要精确控制 SSE 事件发送时机的集成测试。与基于 wiremock 的模拟不同，该服务器支持通过 `oneshot::Receiver` 信号（gate）逐块控制 SSE 事件的发送时序，使测试能够验证流式传输中途的行为。同时支持 `GET /v1/models` 端点返回空列表。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `StreamingSseChunk` | struct | SSE 数据块，包含可选的 `gate`（`oneshot::Receiver<()>`）和 `body` 字符串 |
| `StreamingSseServer` | struct | 服务器句柄，持有 URI、请求记录、shutdown 信号和后台任务 |
| `StreamingSseState` | struct (private) | 内部状态，维护待发送的响应队列和完成通知发送端 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_streaming_sse_server` | `async fn(responses: Vec<Vec<StreamingSseChunk>>) -> (StreamingSseServer, Vec<oneshot::Receiver<i64>>)` | 启动服务器，返回句柄和每个响应流的完成时间戳接收端 |
| `StreamingSseServer::uri` | `fn(&self) -> &str` | 获取服务器 URI |
| `StreamingSseServer::requests` | `async fn(&self) -> Vec<Vec<u8>>` | 获取所有捕获的请求体 |
| `StreamingSseServer::shutdown` | `async fn(self)` | 关闭服务器 |

## 依赖关系

- **引用的 crate**: `tokio`（`TcpListener`、`AsyncReadExt`、`AsyncWriteExt`）
- **被引用方**: 被需要精确控制 SSE 流时序的集成测试使用（通过 `test_codex::build_with_streaming_server`）

## 实现备注

- 服务器完全基于原生 TCP 实现，手动解析 HTTP 请求行、头部和 Content-Length，不依赖任何 HTTP 框架
- 每个 `StreamingSseChunk` 的 `gate` 为 `None` 时立即发送；为 `Some(receiver)` 时等待信号后再发送，实现精确的时序控制
- 响应完成后通过 `oneshot::Sender<i64>` 发送 Unix 毫秒时间戳，供测试验证完成顺序
- 模块内含完整的单元测试，覆盖 GET/POST 路由、SSE 流序、gate 信号阻塞、多响应 FIFO、404/400 错误等场景
