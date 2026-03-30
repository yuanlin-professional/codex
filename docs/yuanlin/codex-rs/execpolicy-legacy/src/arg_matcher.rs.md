# `arg_matcher.rs` — 命令行参数匹配模式定义

## 功能概述

定义了命令行参数匹配器 `ArgMatcher` 枚举，用于在策略检查中对命令参数进行模式匹配。支持字面量字符串、可读/可写文件、正整数、sed 命令等多种匹配类型。同时实现了与 Starlark 运行时的集成，使匹配器能够在策略 DSL 中直接使用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ArgMatcher` | 枚举 | 参数匹配模式，包含 Literal、OpaqueNonFile、ReadableFile、WriteableFile、ReadableFiles、ReadableFilesOrCwd、PositiveInteger、SedCommand、UnverifiedVarargs 等变体 |
| `ArgMatcherCardinality` | 枚举 | 匹配器基数，表示匹配单个参数 (One)、至少一个 (AtLeastOne) 或零到多个 (ZeroOrMore) |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `cardinality` | `&self -> ArgMatcherCardinality` | 返回该匹配器接受的参数个数类型 |
| `arg_type` | `&self -> ArgType` | 将匹配器转换为对应的参数类型 |
| `is_exact` | `&self -> Option<usize>` | 如果基数是精确值则返回 Some(n)，否则返回 None |

## 依赖关系

- **引用的 crate**: `starlark`、`allocative`、`derive_more`
- **被引用方**: `arg_resolver.rs`、`policy_parser.rs`、`program.rs`、`error.rs`

## 实现备注

- 实现了 Starlark 的 `StarlarkValue`、`AllocValue`、`UnpackValue` trait，支持在策略 DSL 中作为值使用。
- `UnpackValue` 实现允许将普通字符串自动转换为 `ArgMatcher::Literal`。
