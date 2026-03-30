# `escalate_protocol.rs` — 权限提升协议消息定义

## 功能概述
定义 shell 权限提升协议中所有消息类型和决策枚举。包括客户端发送的 `EscalateRequest`（exec 拦截请求）、服务端回复的 `EscalateResponse`（Run/Escalate/Deny）、fd 传递消息 `SuperExecMessage` 和执行结果 `SuperExecResult`。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `EscalateRequest` | 结构体 | 拦截的 exec 请求：file, argv, workdir, env |
| `EscalateResponse` | 结构体 | 服务端响应，包含 EscalateAction |
| `EscalationDecision` | 枚举 | 内部决策：Run/Escalate(Execution)/Deny |
| `EscalationExecution` | 枚举 | 提升执行方式：Unsandboxed/TurnDefault/Permissions |
| `EscalateAction` | 枚举 | 线上动作：Run/Escalate/Deny |
| `SuperExecMessage` | 结构体 | 客户端 fd 传递消息 |
| `SuperExecResult` | 结构体 | 提升执行的退出码 |

## 依赖关系
- **引用的 crate**: `serde`, `codex_protocol`, `codex_utils_absolute_path`
- **被引用方**: `escalate_client.rs`, `escalate_server.rs`
