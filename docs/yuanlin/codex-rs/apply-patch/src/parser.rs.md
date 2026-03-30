# `parser.rs` -- apply-patch 补丁文本解析器

## 功能概述

本文件实现了 apply-patch 自定义补丁格式的完整解析器。它将补丁文本字符串解析为结构化的 Hunk 列表，支持三种操作类型：添加文件（Add File）、删除文件（Delete File）和更新文件（Update File，含可选的 Move to 和多个 change chunk）。解析器支持严格模式和宽松模式，宽松模式可处理 GPT-4.1 生成的 heredoc 包装格式。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ParseError` | enum | 解析错误类型：InvalidPatchError（整体格式错误）和 InvalidHunkError（具体 hunk 行号处的错误） |
| `Hunk` | enum | 补丁操作类型：AddFile（含 path 和 contents）、DeleteFile（含 path）、UpdateFile（含 path、move_path 和 chunks） |
| `UpdateFileChunk` | struct | 单个更新 chunk，含 change_context（@@行）、old_lines、new_lines 和 is_end_of_file |
| `ParseMode` | enum (private) | 解析模式：Strict（严格）和 Lenient（宽松，处理 heredoc 包装） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_patch` | `fn(patch) -> Result<ApplyPatchArgs, ParseError>` | 公开 API：解析补丁文本，当前使用宽松模式 |
| `parse_patch_text` | `fn(patch, mode) -> Result<ApplyPatchArgs, ParseError>` | 内部实现：根据模式解析补丁 |
| `check_patch_boundaries_strict` | `fn(lines) -> Result<(), ParseError>` | 验证首行为 `*** Begin Patch`、末行为 `*** End Patch` |
| `check_patch_boundaries_lenient` | `fn(lines, error) -> Result<&[&str], ParseError>` | 宽松模式：处理 `<<EOF...EOF` 包装 |
| `parse_one_hunk` | `fn(lines, line_number) -> Result<(Hunk, usize), ParseError>` | 解析单个 hunk（Add/Delete/Update） |
| `parse_update_file_chunk` | `fn(lines, line_number, allow_missing_context) -> Result<(UpdateFileChunk, usize), ParseError>` | 解析 Update 操作中的单个 change chunk |

## 依赖关系

- **引用的 crate**: `thiserror`
- **引用的内部模块**: `crate::ApplyPatchArgs`
- **被引用方**: `lib.rs`（核心 patch 应用）、`invocation.rs`（命令识别）

## 实现备注

- 补丁格式基于自定义的 Lark-like 语法，以 `*** Begin Patch` / `*** End Patch` 包裹。
- 变更行前缀：`+` 为新增行、`-` 为删除行、` `（空格）为上下文行、空行视为空的上下文行。
- `@@ context` 标记用于定位 chunk 在文件中的位置；第一个 chunk 可省略 @@ 标记。
- `*** End of File` 标记表示 chunk 应匹配文件末尾。
- 宽松模式支持 `<<EOF`、`<<'EOF'` 和 `<<"EOF"` 三种 heredoc 引用格式。
- `PARSE_IN_STRICT_MODE` 常量当前为 false，即默认使用宽松模式。
- 文件级测试（非模块内 #[cfg(test)]）直接验证解析器的各种正确和错误场景。
