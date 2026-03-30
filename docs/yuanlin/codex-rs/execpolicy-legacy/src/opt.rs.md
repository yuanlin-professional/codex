# `opt.rs` — 命令行选项（Option/Flag）定义

## 功能概述

定义了命令行选项的数据模型，包括带值选项和布尔标志。`Opt` 结构体表示一个命令行选项（如 `-n` 或 `--help`），`OptMeta` 枚举区分标志（不带值）和值选项（带一个参数值）。支持标记选项为必需项，并与 Starlark 运行时集成，使其可在策略 DSL 中定义。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Opt` | 结构体 | 命令行选项定义，包含选项名称 `opt`、元数据 `meta` 和是否必需 `required` |
| `OptMeta` | 枚举 | 选项元数据：`Flag`（无值标志）或 `Value(ArgType)`（带值，需要指定参数类型） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Opt::new` | `(opt, meta, required) -> Self` | 构造选项实例 |
| `Opt::name` | `(&self) -> &str` | 返回选项名称 |

## 依赖关系

- **引用的 crate**: `starlark`、`allocative`、`derive_more`
- **被引用方**: `policy_parser.rs`（在 DSL 的 `opt()` 和 `flag()` 函数中创建）、`program.rs`

## 实现备注

- 实现了 Starlark 的 `StarlarkValue`、`UnpackValue`、`AllocValue` trait。
- `UnpackValue` 实现通过 `downcast_ref` 进行克隆提取。
