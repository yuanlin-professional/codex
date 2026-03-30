# `transport/stdio.rs` — Stdio 传输层实现

## 功能概述

本模块实现了基于标准输入/输出的 JSON-RPC 传输。`start_stdio_connection` 创建一个固定的 `ConnectionId(0)` 连接，启动两个异步任务：stdin 读取器逐行读取 JSON-RPC 消息并转发到传输事件通道，stdout 写入器从出站队列接收消息序列化为 JSON 后写入标准输出。每条消息以换行符分隔。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| 无自定义结构体 | — | 仅包含 `start_stdio_connection` 函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_stdio_connection` | `async fn(transport_event_tx, handles) -> IoResult<()>` | 启动 stdio 连接的读写任务 |

## 依赖关系

- **引用的 crate**: `tokio::io`（stdin/stdout 异步 I/O）、上层 `transport` 模块
- **被引用方**: `lib.rs`（stdio 模式启动时调用）

## 实现备注

- stdin EOF 时发送 `ConnectionClosed` 事件，触发主循环的 stdio 单客户端退出逻辑。
- stdout 写入器在通道关闭后退出。
