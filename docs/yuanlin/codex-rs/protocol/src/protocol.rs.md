# `protocol.rs` — Codex 会话核心协议定义

## 功能概述

定义了 Codex 客户端与 Agent 之间的完整会话协议，采用提交队列(SQ)/事件队列(EQ)模式异步通信。包含所有事件消息类型（`EventMsg`）、提交消息类型、沙箱策略、审批配置、会话信息、token 用量、滚动项(rollout item)、回滚、信用快照、Guardian 评估等大量协议级类型和常量。同时 re-export 了 `approvals` 和 `permissions` 模块中的关键类型。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `EventMsg` | 枚举 | 所有从 Agent 到客户端的事件消息类型联合 |
| `SandboxPolicy` | 枚举 | 沙箱策略（ReadOnly / WorkspaceWrite / DangerFullAccess / ExternalSandbox） |
| `WritableRoot` | 结构体 | 可写根目录及其只读保护子路径 |
| `AskForApproval` | 枚举 | 审批策略（Never / OnFailure / Always / UnlessAllowListed） |
| `ReviewDecision` | 枚举 | 用户审批决策（Approved / Abort 等） |
| `RolloutItem` | 结构体 | 滚动项，用于会话历史持久化 |
| `TokenUsage` | 结构体 | 单次 turn 的 token 用量统计 |
| `SessionInfo` | 结构体 | 会话基本信息（ID、模型、协作模式、沙箱等） |
| `FileChange` | 枚举 | 文件变更描述（新增/修改/删除） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| 各种 `pub use` re-export | - | 从 `approvals`、`permissions` 模块导出大量审批和权限类型 |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `serde_json`, `serde_with`, `strum_macros`, `tracing`, `ts_rs`, `codex_git_utils`, `codex_utils_absolute_path`
- **被引用方**: 几乎所有 Codex Rust 子系统（core、tui、app-server 等）

## 实现备注

- 这是 protocol crate 中最大的单文件，定义了整个 SQ/EQ 协议。
- 定义了大量 XML 标签常量（`USER_INSTRUCTIONS_OPEN_TAG` 等），用于在 system prompt 中标记特殊块。
- 包含完整的沙箱策略类型体系及其辅助方法。
- `EventMsg` 枚举涵盖了 Agent 生命周期中所有可能的事件（用户消息、Agent 消息、命令执行、补丁应用、推理、搜索、图片生成、上下文压缩、错误等）。
