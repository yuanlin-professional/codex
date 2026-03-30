# `approvals.rs` — 审批请求与决策协议类型

## 功能概述

定义了 Codex 中与命令执行审批、网络访问审批、文件变更审批、MCP 引出(elicitation)请求及 Guardian 风险评估相关的所有协议类型。这些类型在 Agent 需要用户或自动审批者授权某项操作时使用，涵盖从命令沙箱逃逸、网络策略修改到 MCP 表单确认等各种场景。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Permissions` | 结构体 | 聚合沙箱策略、文件系统策略和网络策略 |
| `EscalationPermissions` | 枚举 | 权限升级配置，可为配置文件或详细权限 |
| `ExecPolicyAmendment` | 结构体 | 提议的执行策略修正（命令前缀白名单） |
| `NetworkApprovalProtocol` | 枚举 | 网络审批的协议类型（Http / Https / Socks5Tcp / Socks5Udp） |
| `NetworkApprovalContext` | 结构体 | 网络审批上下文（主机名 + 协议） |
| `GuardianAssessmentEvent` | 结构体 | Guardian 子Agent 的风险评估事件 |
| `ExecApprovalRequestEvent` | 结构体 | 命令执行审批请求事件 |
| `ElicitationRequest` | 枚举 | MCP 引出请求（Form 表单 / URL 重定向） |
| `ApplyPatchApprovalRequestEvent` | 结构体 | 文件补丁应用审批请求 |
| `ElicitationAction` | 枚举 | 引出请求的用户操作（Accept / Decline / Cancel） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `effective_approval_id` | `fn effective_approval_id(&self) -> String` | 返回实际生效的审批 ID |
| `effective_available_decisions` | `fn effective_available_decisions(&self) -> Vec<ReviewDecision>` | 根据上下文计算可用的审批决策列表 |
| `default_available_decisions` | `fn default_available_decisions(...) -> Vec<ReviewDecision>` | 在无显式决策列表时生成默认可用决策 |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `serde_json`, `ts_rs`, `std::collections::HashMap`, `std::path::PathBuf`
- **被引用方**: `protocol.rs` 通过 `pub use` 导出这些类型

## 实现备注

- `ExecApprovalRequestEvent` 是最复杂的类型，携带命令、工作目录、网络上下文、策略修正提案、额外权限等全部审批上下文。
- `available_decisions` 字段是后加的，兼容老版本发送者时会回退到根据其他字段推导的遗留逻辑。
- Guardian 评估使用 0-100 数值风险评分配合 Low/Medium/High 粗粒度标签。
