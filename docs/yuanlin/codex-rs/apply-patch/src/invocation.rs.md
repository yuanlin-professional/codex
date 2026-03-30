# `invocation.rs` -- apply_patch 的命令行调用解析与验证

## 功能概述

本文件负责从不同格式的命令行参数中识别和解析 apply_patch 调用。它支持直接调用（`apply_patch <patch>`）和 shell heredoc 形式（`bash -lc 'apply_patch <<EOF...'`），以及可选的 `cd <path> &&` 前缀。通过 Tree-sitter Bash 语法分析器精确匹配 heredoc 模式，确保只接受预期的调用格式，拒绝可能导致错误的变体。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ApplyPatchShell` | enum (private) | Shell 类型分类：Unix、PowerShell、Cmd |
| `MaybeApplyPatch` | enum | 解析结果：Body（成功）、ShellParseError、PatchParseError、NotApplyPatch |
| `ExtractHeredocError` | enum | Heredoc 提取错误类型：命令不匹配、语法加载失败、UTF-8 错误、AST 解析失败、找不到 heredoc body |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `maybe_parse_apply_patch` | `fn(argv) -> MaybeApplyPatch` | 解析 argv 判断是否为 apply_patch 调用 |
| `maybe_parse_apply_patch_verified` | `fn(argv, cwd) -> MaybeApplyPatchVerified` | 带文件系统验证的完整解析：解析 patch、读取文件、生成 unified diff、解析 move path |
| `extract_apply_patch_from_bash` | `fn(src) -> Result<(String, Option<String>), ExtractHeredocError>` | 使用 Tree-sitter 从 Bash 脚本中提取 heredoc body 和可选的 cd path |
| `classify_shell` | `fn(shell, flag) -> Option<ApplyPatchShell>` | 根据 shell 名称和参数分类 shell 类型 |
| `parse_shell_script` | `fn(argv) -> Option<(ApplyPatchShell, &str)>` | 从 argv 解析出 shell 类型和脚本内容 |

## 依赖关系

- **引用的 crate**: `tree_sitter`、`tree_sitter_bash`（Bash AST 解析）
- **引用的内部模块**: `parser`（parse_patch、Hunk）、`crate::unified_diff_from_chunks`、`crate::ApplyPatchAction` 等
- **被引用方**: `lib.rs`（re-export `maybe_parse_apply_patch_verified`）、`codex-core`

## 实现备注

- Tree-sitter 查询使用锚点（`.`）确保 heredoc 是脚本中唯一的顶层语句，拒绝 `echo foo; apply_patch...` 或 `apply_patch... && echo done` 等形式。
- 支持 `apply_patch` 和 `applypatch` 两个命令名。
- 支持 bash、zsh、sh（`-lc` / `-c`）、powershell/pwsh（`-Command`，可选 `-NoProfile`）和 cmd（`/c`）。
- `cd` 只接受单个 word 参数（支持单引号、双引号和 raw string），连接符必须是 `&&`（不接受 `;`、`|` 或 `||`）。
- 隐式调用检测：若 argv 为单个 patch 文本或 shell 脚本体为 patch 文本，返回 `ImplicitInvocation` 错误。
- 内嵌测试覆盖了 literal、heredoc、各种 shell 类型、cd 组合、拒绝场景和路径解析等大量用例。
