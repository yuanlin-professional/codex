# `processor.rs` — 连接级消息处理循环

## 功能概述

`run_connection` 函数是每个 WebSocket 连接的主处理循环。它创建 `RpcRouter` 和 `ExecServerHandler`，顺序处理入站消息（保持 initialize/initialized 顺序），将请求路由到对应的处理器，通知路由到通知处理器，并通过独立的出站任务将响应和服务端通知编码发送。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_connection` | `async fn(JsonRpcConnection)` | 连接主循环：路由请求、处理通知、转发响应 |

## 依赖关系

- **引用的 crate**: `tracing`
- **被引用方**: `server/transport.rs`

## 实现备注

- 入站消息顺序处理（不并发），确保协议状态机正确。
- 未知方法返回 `method_not_found` 错误。
- 连接关闭时调用 `handler.shutdown()` 终止所有关联进程。
