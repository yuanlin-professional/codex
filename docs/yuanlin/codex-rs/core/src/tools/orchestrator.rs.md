# `orchestrator.rs` — 工具编排器

## 功能概述
工具执行的中央编排器，驱动审批 -> 沙箱选择 -> 执行 -> 重试的完整流程。对任何 `ToolRuntime` 实现提供统一的编排逻辑：首次在选定沙箱下执行，如果因沙箱拒绝失败，则请求用户审批后在提升权限下重试。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolOrchestrator` | struct | 工具编排器，持有 `SandboxManager` |
| `OrchestratorRunResult<Out>` | struct | 编排结果：output + deferred_network_approval |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run` | `async fn run<Rq, Out, T>(tool, req, tool_ctx, turn_ctx, approval_policy)` | 编排主流程：审批 -> 首次尝试 -> 沙箱拒绝时重试 |
| `run_attempt` | `async fn run_attempt(tool, req, tool_ctx, attempt, ...)` | 单次执行尝试，包含网络审批 |

## 依赖关系
- **引用的 crate**: `codex_sandboxing`, `codex_otel`
- **内部依赖**: `crate::tools::sandboxing`, `crate::tools::network_approval`, `crate::guardian`
- **被引用方**: 各工具处理器中调用 `ToolOrchestrator::run` 执行

## 实现备注
- **审批阶段**: 根据 `ExecApprovalRequirement` 决定 Skip/NeedsApproval/Forbidden
- **首次尝试**: 沙箱类型由 `sandbox_mode_for_first_attempt` 决定，可跳过沙箱
- **重试逻辑**: 仅在 `SandboxErr::Denied` 时触发，且需要 `escalate_on_failure() && wants_no_sandbox_approval()`
- **网络审批**: Immediate 模式在首次尝试后检查结果，Deferred 模式在失败时清理
- **OTel 集成**: 记录审批决策的来源（User/AutomatedReviewer/Config）
- Never/OnRequest 策略下不进行无沙箱重试
