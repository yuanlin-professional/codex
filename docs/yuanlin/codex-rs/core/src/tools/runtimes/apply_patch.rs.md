# `apply_patch.rs` — Apply Patch 运行时

## 功能概述
实现了 `ApplyPatchRuntime`，负责在编排器（orchestrator）下执行经过验证的补丁操作。该运行时复用上游已完成的审批决策，构建 `codex --codex-run-as-apply-patch` 自调用命令，并在当前沙箱策略下执行。支持 Guardian 审批流程和文件级别的会话缓存审批。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ApplyPatchRequest` | struct | 补丁请求：包含 action、file_paths、changes、审批需求、超时等 |
| `ApplyPatchRuntime` | struct | 补丁执行运行时，实现 `Sandboxable`、`Approvable`、`ToolRuntime` trait |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_sandbox_command` | `fn build_sandbox_command(req, codex_self_exe)` | 构建沙箱命令，Unix/Windows 分别实现 |
| `start_approval_async` | `fn start_approval_async(req, ctx) -> BoxFuture<ReviewDecision>` | 异步审批流程，支持预审批、Guardian、缓存审批 |
| `run` | `async fn run(req, attempt, ctx)` | 执行补丁命令并返回输出 |
| `exec_approval_requirement` | `fn exec_approval_requirement(req)` | 返回请求自带的审批需求（由上游 `assess_patch_safety` 决定） |

## 依赖关系
- **引用的 crate**: `codex_apply_patch`, `codex_sandboxing`, `codex_protocol`, `futures`
- **内部依赖**: `crate::tools::sandboxing::*`, `crate::exec`, `crate::guardian`
- **被引用方**: `tools/handlers/apply_patch.rs`

## 实现备注
- 补丁审批键使用 `AbsolutePathBuf` 文件路径，支持多文件批量审批的会话缓存
- `permissions_preapproved` 为 `true` 且无重试原因时直接返回 `Approved`
- Unix 上优先使用 `codex_self_exe`，回退到 `std::env::current_exe()`
- 补丁以最小化环境运行，确保确定性并避免信息泄露
