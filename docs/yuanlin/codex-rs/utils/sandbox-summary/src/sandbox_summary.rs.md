# `sandbox_summary.rs` — 沙箱策略摘要格式化

## 功能概述

将 `SandboxPolicy` 枚举转换为人类可读的摘要字符串。不同策略格式化方式：`DangerFullAccess` 输出 `"danger-full-access"`，`ReadOnly` 和 `ExternalSandbox` 可选附加网络访问后缀，`WorkspaceWrite` 列出可写根目录列表及网络访问状态。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `summarize_sandbox_policy` | `fn(sandbox_policy: &SandboxPolicy) -> String` | 格式化沙箱策略为摘要字符串 |

## 依赖关系

- **引用的 crate**: `codex_protocol`
- **被引用方**: `config_summary.rs`, TUI 显示

## 实现备注

- `WorkspaceWrite` 默认包含 `workdir`，条件包含 `/tmp` 和 `$TMPDIR`。
- 测试覆盖了所有策略变体及网络访问组合。
