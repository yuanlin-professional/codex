# `sandboxing.rs` — 沙箱与审批 trait 定义

## 功能概述
定义了工具运行时共享的审批和沙箱化 trait 体系。这是整个工具编排系统的类型契约层，包括审批存储（`ApprovalStore`）、审批上下文（`ApprovalCtx`）、审批需求（`ExecApprovalRequirement`）、沙箱尝试（`SandboxAttempt`）以及运行时 trait（`ToolRuntime`、`Approvable`、`Sandboxable`）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ApprovalStore` | struct | 序列化键的审批决策缓存 |
| `ApprovalCtx<'a>` | struct | 审批上下文：session、turn、call_id、retry_reason、network_approval_context |
| `ExecApprovalRequirement` | enum | 审批需求：Skip / NeedsApproval / Forbidden |
| `SandboxOverride` | enum | 沙箱覆盖：NoOverride / BypassSandboxFirstAttempt |
| `ToolCtx` | struct | 工具上下文：session、turn、call_id、tool_name |
| `ToolError` | enum | 工具错误：Rejected(String) / Codex(CodexErr) |
| `SandboxAttempt<'a>` | struct | 沙箱尝试配置：sandbox 类型、各策略、manager 引用 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `with_cached_approval` | `async fn(services, tool_name, keys, fetch)` | 带会话缓存的审批：全部已批准则跳过，否则执行审批并缓存 |
| `default_exec_approval_requirement` | `fn(policy, file_system_sandbox_policy)` | 根据审批策略和文件系统策略计算默认审批需求 |
| `sandbox_override_for_first_attempt` | `fn(sandbox_permissions, exec_approval_requirement)` | 决定首次尝试是否跳过沙箱 |
| `SandboxAttempt::env_for` | `fn(&self, command, options, network)` | 通过 SandboxManager 转换命令为沙箱化的 ExecRequest |

## 依赖关系
- **引用的 crate**: `codex_sandboxing`, `codex_protocol`, `futures`, `serde`
- **内部依赖**: `crate::codex::Session`, `crate::state::SessionServices`
- **被引用方**: 所有运行时（shell、apply_patch、unified_exec）和编排器

## 实现备注
- `Approvable` trait 支持多键审批（apply_patch 按文件路径），只有全部已批准才跳过
- `ExecApprovalRequirement::Skip { bypass_sandbox: true }` 表示 ExecPolicy `Allow` 隐含完全信任
- Granular 策略中 `sandbox_approval=false` 会产生 `Forbidden` 需求
- `SandboxAttempt::env_for` 是将高层抽象映射到底层 `ExecRequest` 的关键桥梁

## trait 层次
```
Sandboxable (沙箱偏好)
    + Approvable<Req> (审批逻辑)
        = ToolRuntime<Req, Out> (完整运行时：网络审批 + 执行)
```
