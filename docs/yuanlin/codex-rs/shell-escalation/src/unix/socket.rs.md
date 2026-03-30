# `socket.rs` — 异步 Unix Socket 通信层

## 功能概述
封装基于 socket2 和 tokio 的异步 Unix socket 通信，支持流式（SOCK_STREAM）和数据报（SOCK_DGRAM）两种模式。实现帧协议（长度前缀+负载）的读写，以及通过 SCM_RIGHTS 控制消息传递文件描述符。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `AsyncSocket` | 结构体 | 流式 socket，支持帧协议和 fd 传递 |
| `AsyncDatagramSocket` | 结构体 | 数据报 socket，用于握手消息 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `send_with_fds` | `async fn(msg, fds) -> io::Result<()>` | 发送序列化消息和文件描述符 |
| `receive_with_fds` | `async fn() -> io::Result<(T, Vec<OwnedFd>)>` | 接收消息和文件描述符 |
| `pair` | `fn() -> io::Result<(Self, Self)>` | 创建配对的 socket 端点 |

## 依赖关系
- **引用的 crate**: `socket2`, `tokio`, `serde`, `libc`
- **被引用方**: `escalate_client.rs`, `escalate_server.rs`
