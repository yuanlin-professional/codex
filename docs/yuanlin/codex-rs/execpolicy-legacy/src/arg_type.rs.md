# `arg_type.rs` — 参数类型定义与值验证

## 功能概述

定义了命令行参数的类型系统 `ArgType` 枚举，用于对匹配后的参数值进行类型验证。每种类型都有对应的验证逻辑，能够判断参数值是否合法（如文件名不能为空、正整数必须大于零等），以及是否可能引发文件写入操作。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ArgType` | 枚举 | 参数类型，包含 Literal、OpaqueNonFile、ReadableFile、WriteableFile、PositiveInteger、SedCommand、Unknown |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `validate` | `&self, value: &str -> Result<()>` | 验证给定值是否符合当前类型的约束 |
| `might_write_file` | `&self -> bool` | 判断此类型的参数是否可能导致文件写入（仅 WriteableFile 和 Unknown 返回 true） |

## 依赖关系

- **引用的 crate**: `starlark`、`allocative`、`derive_more`、`serde`
- **被引用方**: `arg_matcher.rs`、`valid_exec.rs`、`execv_checker.rs`

## 实现备注

- `SedCommand` 类型的验证委托给 `sed_command::parse_sed_command` 函数。
- 实现了 Starlark 的 `StarlarkValue` trait 以便在策略 DSL 中使用。
