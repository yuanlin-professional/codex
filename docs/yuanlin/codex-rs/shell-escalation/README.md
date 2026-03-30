# shell-escalation

## 功能概述

`codex-shell-escalation` 实现了 Shell 命令权限升级协议。当 Codex 需要在受限沙箱环境中执行命令时，沙箱内的进程可能无法直接执行某些需要更高权限的操作。该 crate 提供了一套基于 Unix 域套接字的通信协议，允许沙箱内的 exec 包装器向沙箱外的服务器请求"升级"执行权限。

核心场景：当 Shell 中的 `exec()` 调用被拦截时，exec 包装器通过 `CODEX_ESCALATE_SOCKET` 环境变量指定的 Unix 套接字发送升级请求，服务器根据策略决定是让命令在沙箱内直接执行（Run），还是在服务器端代为执行（Escalate）。

该 crate 仅支持 Unix 平台（通过 `#[cfg(unix)]` 条件编译）。

## 架构说明

```
升级执行流程:

  Command     Server     Shell     Execve Wrapper
               |
               o-------->o
               |          |
               |          o--(exec)-->o
               |          |           |
               |o<-(EscalateReq)------o
               ||         |           |
               |o--(Escalate)-------->o
               ||         |           |
               |o<-----------(fds)----o
               ||         |           |
    o<----------o         |           |
    |          ||         |           |
    x---------->o         |           |
               ||         |           |
               |x--(exit code)------->o
               |          |           |
               |          o<--(exit)--x
               |          |
               o<---------x

非升级执行流程 (直接运行):

  Server     Shell     Execve Wrapper     Command
    |
    o-------->o
    |         |
    |         o--(exec)-->o
    |         |           |
    |o<-(EscalateReq)-----o
    ||        |           |
    |o--(Run)------------>o
    |         |           |
    |         |           x--(exec)-->o
    |         |                       |
    |         o<--------------(exit)--x
    |         |
    o<--------x
```

```
模块依赖关系:

  +---------------------+
  |   EscalateServer    | (服务端: 监听升级请求)
  +---------+-----------+
            |
            v
  +---------+-----------+
  |  EscalationPolicy   | (策略引擎: 决定 Run 或 Escalate)
  +---------------------+
            |
            v
  +---------------------+     +---------------------+
  | escalate_protocol   |<--->|  escalate_client    |
  | (协议消息定义)       |     | (exec 包装器客户端)  |
  +---------------------+     +---------------------+
            |
            v
  +---------------------+
  |       socket        | (Unix 域套接字工具)
  +---------------------+
```

## 目录结构

```
shell-escalation/
  src/
    lib.rs                          # 库入口 (条件编译 Unix 模块)
    bin/
      main_execve_wrapper.rs        # codex-execve-wrapper 二进制入口
    unix/
      mod.rs                        # Unix 模块聚合与导出
      escalate_server.rs            # 升级服务器实现
      escalate_client.rs            # exec 包装器客户端实现
      escalate_protocol.rs          # 协议消息类型定义
      escalation_policy.rs          # 升级策略逻辑
      execve_wrapper.rs             # execve 包装器核心实现
      socket.rs                     # Unix 域套接字工具函数
      stopwatch.rs                  # 计时器工具
  Cargo.toml
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-protocol` | 审批权限等协议类型（EscalationPermissions） |
| `codex-utils-absolute-path` | 绝对路径处理 |
| `tokio` | 异步运行时（网络 I/O、进程管理、信号处理） |
| `tokio-util` | Tokio 扩展工具 |
| `socket2` | 底层套接字操作 |
| `libc` | Unix 系统调用 |
| `async-trait` | 异步 trait 支持 |
| `clap` | CLI 参数解析 |
| `serde` / `serde_json` | 协议消息序列化 |
| `tracing` / `tracing-subscriber` | 结构化日志 |
| `anyhow` | 错误处理 |

## 核心接口/API

### `EscalateServer`

升级请求服务器，监听 Unix 域套接字上的升级请求：

```rust
pub struct EscalateServer { ... }
```

### `EscalationSession`

表示一个升级会话的上下文。

### `ShellCommandExecutor`

Shell 命令执行器，负责在服务端实际执行升级后的命令。

### `ExecParams` / `ExecResult` / `PreparedExec`

```rust
pub struct ExecParams { ... }   // exec 参数
pub struct ExecResult { ... }   // exec 执行结果
pub struct PreparedExec { ... } // 准备好的 exec 调用
```

### `EscalationPolicy`

策略决策引擎，决定对某个命令是执行 Run 还是 Escalate。

### `EscalateAction` / `EscalationDecision` / `EscalationExecution`

协议层消息类型：

```rust
pub enum EscalateAction { ... }        // 升级动作类型
pub struct EscalationDecision { ... }  // 升级决策
pub struct EscalationExecution { ... } // 升级执行结果
```

### `EscalationPermissions` / `Permissions`

从 `codex-protocol` re-export 的权限类型。

### 环境变量

```rust
pub const ESCALATE_SOCKET_ENV_VAR: &str; // "CODEX_ESCALATE_SOCKET"
```

exec 包装器通过此环境变量找到升级服务器的 Unix 域套接字路径。

### 二进制工具

`codex-execve-wrapper`：作为 exec 包装器注入到沙箱环境中，拦截所有 `exec()` 调用并通过升级协议与服务器通信。
