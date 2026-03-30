# `additional_dirs.rs` — 额外目录参数的沙箱策略警告生成

## 功能概述

本文件负责在用户通过 `--add-dir` 指定额外可写目录时，根据当前生效的沙箱策略判断这些目录是否会被忽略，并生成相应的警告消息。当沙箱策略为只读（ReadOnly）模式时，额外目录参数将被忽略并产生警告；而在 WorkspaceWrite、DangerFullAccess 或 ExternalSandbox 模式下不会产生警告。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `add_dir_warning_message` | `(additional_dirs: &[PathBuf], sandbox_policy: &SandboxPolicy) -> Option<String>` | 根据沙箱策略判断是否需要生成 `--add-dir` 被忽略的警告消息 |
| `format_warning` | `(additional_dirs: &[PathBuf]) -> String` | 格式化包含所有被忽略目录路径的警告字符串 |

## 依赖关系

- **引用的 crate**: `codex_protocol::protocol::SandboxPolicy`、`std::path::PathBuf`
- **被引用方**: `lib.rs` 中在 `run_main` 启动时调用以检查额外目录参数的有效性

## 实现备注

模块内包含完整的单元测试，覆盖了所有沙箱策略变体的行为：WorkspaceWrite 返回 None、DangerFullAccess 返回 None、ExternalSandbox 返回 None、ReadOnly 返回警告、空目录列表返回 None。
