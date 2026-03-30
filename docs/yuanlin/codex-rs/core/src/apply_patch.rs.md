# `apply_patch.rs` — apply_patch 工具调用处理与安全评估

## 功能概述

此文件是 `apply_patch` 工具调用的处理层，负责将补丁安全评估结果转换为具体的执行路径。根据 `safety.rs` 的评估结果，决定是直接输出（用户明确批准或被拒绝）、还是委托给执行引擎处理（可能需要也可能不需要用户审批）。文件还提供了将 `ApplyPatchAction` 转换为协议层 `FileChange` 的功能。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `InternalApplyPatchInvocation` | 枚举 | 补丁调用结果：`Output`（直接输出）或 `DelegateToExec`（委托执行） |
| `ApplyPatchExec` | 结构体 | 委托执行的补丁信息：action、auto_approved、exec_approval_requirement |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `apply_patch` | `async (turn_context, fs_policy, action) -> InternalApplyPatchInvocation` | 评估补丁安全性并返回处理路径 |
| `convert_apply_patch_to_protocol` | `(action) -> HashMap<PathBuf, FileChange>` | 将 ApplyPatchAction 转换为协议层的 FileChange 映射 |

## 依赖关系

- **引用的 crate**: `crate::safety`（安全评估）、`codex_apply_patch`、`crate::function_tool`、`crate::tools::sandboxing`
- **被引用方**: 工具调用处理流程中的 apply_patch 分支

## 实现备注

- `AutoApprove` 时委托执行，`bypass_sandbox = false` 保持沙箱隔离
- `AskUser` 时也委托执行，将审批提示留给工具运行时处理，与 shell/unified_exec 审批流程一致
- `Reject` 时直接返回错误输出给模型
- `auto_approved` 字段反转了 `user_explicitly_approved`，因为二者语义相反
