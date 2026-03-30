# `lib.rs` — execpolicy-legacy 库入口与公开 API

## 功能概述

作为 `codex_execpolicy_legacy` crate 的入口文件，声明所有内部模块并统一导出公开 API。包含默认策略文件的内嵌加载功能，通过 `include_str!` 将编译时的 `default.policy` 文件内容嵌入二进制中，并提供 `get_default_policy()` 函数用于获取解析后的默认策略。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| (公开导出) | 各种类型 | 导出了 ArgMatcher、ArgType、Error、ExecCall、ExecvChecker、Policy、PolicyParser、ProgramSpec、ValidExec、MatchedArg、MatchedOpt、MatchedFlag 等核心类型 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `get_default_policy` | `() -> starlark::Result<Policy>` | 解析并返回内嵌的默认策略 |

## 依赖关系

- **引用的 crate**: `starlark`
- **被引用方**: `main.rs`、所有测试文件

## 实现备注

- 使用 `#[macro_use] extern crate starlark` 引入 Starlark 宏。
- 默认策略内容通过 `include_str!("default.policy")` 在编译期嵌入。
