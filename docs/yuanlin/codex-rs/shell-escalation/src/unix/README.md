# shell-escalation/src/unix — Unix 实现

## 功能概述
Unix 平台的 Shell 权限提升实现。

## 目录结构
| 文件 | 说明 |
|------|------|
| `escalate_client.rs` | 提权客户端 |
| `escalate_protocol.rs` | 提权协议 |
| `escalate_server.rs` | 提权服务器 |
| `escalation_policy.rs` | 提权策略 |
| `execve_wrapper.rs` | execve 包装器 |
| `mod.rs` | 模块入口 |
| `socket.rs` | Socket 通信 |
| `stopwatch.rs` | 计时器 |
