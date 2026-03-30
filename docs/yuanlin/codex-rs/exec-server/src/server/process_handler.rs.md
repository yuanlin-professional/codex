# `process_handler.rs` — 服务端进程请求处理器

## 功能概述

`ProcessHandler` 封装 `LocalProcess`，为服务端提供进程管理方法（initialize、initialized、exec、exec_read、exec_write、terminate）的代理层。负责转发初始化状态检查和所有进程 RPC 调用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ProcessHandler` | struct | 包装 LocalProcess 的服务端进程处理器 |

## 依赖关系

- **被引用方**: `server/handler.rs`
