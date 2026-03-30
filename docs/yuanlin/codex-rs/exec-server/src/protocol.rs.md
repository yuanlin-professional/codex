# `protocol.rs` — exec-server JSON-RPC 协议定义

## 功能概述

定义 exec-server 全部 JSON-RPC 方法名常量和协议消息类型，包括初始化握手（initialize/initialized）、进程管理（process/start、process/read、process/write、process/terminate）、进程事件通知（process/output、process/exited、process/closed）以及文件系统操作（fs/*）。所有字节数据使用 base64 编码的 `ByteChunk` 类型传输。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ByteChunk` | struct | base64 编码的字节块 |
| `ExecParams` / `ExecResponse` | struct | 进程启动参数和响应 |
| `ReadParams` / `ReadResponse` | struct | 输出读取参数和响应（含 chunks、exit_code、closed） |
| `WriteParams` / `WriteResponse` | struct | 写入参数和响应（含 WriteStatus） |
| `ExecOutputStream` | enum | 输出流类型：Stdout/Stderr/Pty |
| `ExecOutputDeltaNotification` | struct | 输出增量通知 |
| `ExecExitedNotification` | struct | 进程退出通知 |
| `ExecClosedNotification` | struct | 进程关闭通知 |

## 依赖关系

- **引用的 crate**: `serde`, `base64`
- **被引用方**: `client.rs`, `local_process.rs`, `server/` 各模块

## 实现备注

- 使用自定义 `base64_bytes` serde 模块实现二进制数据的 base64 序列化/反序列化。
- 所有结构体使用 `camelCase` JSON 命名约定。
