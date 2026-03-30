# `policy_parser.rs` — Starlark 策略文件解析器

## 功能概述

将使用 Starlark DSL 编写的策略文件解析为内存中的 `Policy` 对象。解析器注册了一系列内建函数（`define_program`、`forbid_substrings`、`forbid_program_regex`、`opt`、`flag`），并将预定义的参数匹配器常量（如 `ARG_RFILE`、`ARG_WFILE` 等）注入到 Starlark 评估环境中。使用 `PolicyBuilder` 在评估过程中逐步收集程序规范。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PolicyParser` | 结构体 | 策略解析器，封装策略源标识和未解析的策略文本 |
| `PolicyBuilder` | 结构体 (内部) | 在 Starlark 评估期间收集 ProgramSpec、禁止正则和禁止子串 |
| `ForbiddenProgramRegex` | 结构体 | 禁止程序的正则匹配规则，包含正则表达式和原因 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `PolicyParser::new` | `(policy_source, unparsed_policy) -> Self` | 创建解析器实例 |
| `PolicyParser::parse` | `(&self) -> starlark::Result<Policy>` | 解析策略文件并返回 Policy 对象 |
| `define_program` | (Starlark 内建) | 定义一个程序规范，包括参数模式、选项、系统路径等 |
| `forbid_substrings` | (Starlark 内建) | 禁止参数中包含指定子串 |
| `forbid_program_regex` | (Starlark 内建) | 通过正则表达式禁止特定程序 |
| `opt` | (Starlark 内建) | 定义带值选项 |
| `flag` | (Starlark 内建) | 定义布尔标志 |

## 依赖关系

- **引用的 crate**: `starlark`、`multimap`、`regex_lite`、`log`
- **被引用方**: `lib.rs`（`get_default_policy`）、`main.rs`

## 实现备注

- 使用 Starlark 的 Extended 方言并启用 f-string 支持。
- `PolicyBuilder` 通过 `Evaluator.extra` 在评估过程中传递，实现了 `ProvidesStaticType` trait。
