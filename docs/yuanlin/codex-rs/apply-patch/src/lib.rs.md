# `lib.rs` -- apply-patch crate 的库入口与核心 patch 应用逻辑

## 功能概述

本文件是 `codex-apply-patch` crate 的库入口，定义了 patch 应用的核心公开 API 和内部实现。它包含 patch 解析、hunk 应用、unified diff 生成、文件系统操作以及错误类型定义。该 crate 既可作为独立 CLI 工具使用，也可作为库被 Codex 核心集成。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ApplyPatchError` | enum | 错误类型：ParseError（解析错误）、IoError（IO 错误）、ComputeReplacements（替换计算错误）、ImplicitInvocation（缺少显式调用） |
| `IoError` | struct | 带上下文描述的 IO 错误包装 |
| `ApplyPatchArgs` | struct | 解析后的 patch 参数，含原始 patch 文本、hunk 列表和可选工作目录 |
| `ApplyPatchFileChange` | enum | 文件变更类型：Add（添加）、Delete（删除）、Update（更新，含 unified diff 和 move_path） |
| `MaybeApplyPatchVerified` | enum | 验证结果：Body（成功解析）、ShellParseError、CorrectnessError、NotApplyPatch |
| `ApplyPatchAction` | struct | 验证后的 patch 操作，含 changes map（绝对路径）、原始 patch 和 cwd |
| `AffectedPaths` | struct | 受影响的文件路径分类：added、modified、deleted |
| `ApplyPatchFileUpdate` | struct | 文件更新结果，含 unified_diff 和最终 content |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `apply_patch` | `fn(patch, stdout, stderr) -> Result<(), ApplyPatchError>` | 顶层 API：解析 patch 并应用到文件系统 |
| `apply_hunks` | `fn(hunks, stdout, stderr) -> Result<(), ApplyPatchError>` | 应用已解析的 hunk 列表 |
| `apply_hunks_to_files` | `fn(hunks) -> Result<AffectedPaths>` | 将 hunk 应用到文件系统并返回受影响路径 |
| `derive_new_contents_from_chunks` | `fn(path, chunks) -> Result<AppliedPatch>` | 读取文件并根据 chunk 计算新内容 |
| `compute_replacements` | `fn(lines, path, chunks) -> Result<Vec<replacement>>` | 计算替换列表（start_index, old_len, new_lines） |
| `apply_replacements` | `fn(lines, replacements) -> Vec<String>` | 按倒序应用替换到行列表 |
| `unified_diff_from_chunks` | `fn(path, chunks) -> Result<ApplyPatchFileUpdate>` | 从 chunks 生成 unified diff 格式的差异 |
| `print_summary` | `fn(affected, out) -> io::Result<()>` | 以 git 风格（A/M/D）打印变更摘要 |

## 依赖关系

- **引用的 crate**: `anyhow`、`thiserror`、`similar`（TextDiff 用于生成 unified diff）
- **引用的内部模块**: `parser`（Hunk、parse_patch）、`seek_sequence`、`invocation`
- **被引用方**: `standalone_executable`（CLI 入口）、`codex-core`（通过 `maybe_parse_apply_patch_verified` 集成）

## 实现备注

- 替换操作按倒序（从文件末尾到开头）执行，避免前面的替换影响后面的位置。
- 文件更新后始终确保以换行符结尾。
- 尾部空行在 split('\n') 后会被剥离，以匹配标准 diff 的行计数行为。
- `CODEX_CORE_APPLY_PATCH_ARG1` 常量用于 Codex 可执行文件自调用 apply_patch 时的特殊标记。
- `APPLY_PATCH_TOOL_INSTRUCTIONS` 从外部 md 文件编译时嵌入，供 GPT-4.1 使用。
- 内嵌测试覆盖了添加、删除、更新、移动、多 chunk、纯添加 chunk、Unicode dash 模糊匹配、unified diff 生成等大量场景。
