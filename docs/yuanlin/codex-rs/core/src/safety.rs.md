# `safety.rs` — apply_patch 安全检查与审批策略

## 功能概述

此文件实现了 `apply_patch` 操作的安全评估逻辑，根据用户的审批策略（`AskForApproval`）、沙箱策略（`SandboxPolicy`）和文件系统写入范围来决定补丁是否可以自动批准、需要用户确认还是应被拒绝。核心逻辑是判断补丁的所有文件变更是否限制在允许的可写路径范围内。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SafetyCheck` | 枚举 | 安全检查结果：`AutoApprove`（自动批准，含沙箱类型）、`AskUser`（需用户确认）、`Reject`（拒绝，含原因） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `assess_patch_safety` | `(action, policy, sandbox_policy, fs_policy, cwd, windows_level) -> SafetyCheck` | 综合评估补丁安全性 |
| `is_write_patch_constrained_to_writable_paths` | `(action, fs_policy, cwd) -> bool` | 判断补丁的所有写操作是否在可写路径范围内 |

## 依赖关系

- **引用的 crate**: `codex_apply_patch`（`ApplyPatchAction`、`ApplyPatchFileChange`）、`codex_sandboxing`、`codex_protocol`
- **被引用方**: `apply_patch.rs` 调用 `assess_patch_safety` 进行安全评估

## 实现备注

- 空补丁直接拒绝
- `OnFailure` 策略总是自动批准（即使路径不在可写范围内）
- `UnlessTrusted` 策略始终要求用户确认
- `Never` 和 `Granular(!sandbox_approval)` 在无法使用沙箱时拒绝而非询问用户
- `DangerFullAccess` 和 `ExternalSandbox` 绕过沙箱机制直接批准
- 路径规范化使用纯逻辑处理（不访问文件系统），支持不存在的文件路径
- 补丁中的 `Update` 操作还需要检查 `move_path` 目标路径
