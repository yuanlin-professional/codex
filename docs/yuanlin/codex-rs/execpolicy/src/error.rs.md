# `error.rs` — 错误类型定义

## 功能概述

定义了 `execpolicy` crate 的错误类型系统，包括错误枚举 `Error`、文本位置 `TextPosition`、文本范围 `TextRange` 和错误位置 `ErrorLocation`。支持将错误与策略文件中的源码位置关联，便于调试时定位具体的规则定义位置。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Error` | 枚举 | 错误类型，包含 InvalidDecision、InvalidPattern、InvalidExample、InvalidRule、ExampleDidNotMatch、ExampleDidMatch、Starlark 等变体 |
| `TextPosition` | 结构体 | 文本位置（行号和列号，从 1 开始） |
| `TextRange` | 结构体 | 文本范围（起始和结束位置） |
| `ErrorLocation` | 结构体 | 错误在策略文件中的位置（文件路径和范围） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `with_location` | `(self, location) -> Self` | 为 ExampleDidNotMatch 和 ExampleDidMatch 错误附加位置信息 |
| `location` | `(&self) -> Option<ErrorLocation>` | 提取错误的位置信息，包括从 Starlark 错误中提取 |

## 依赖关系

- **引用的 crate**: `starlark`、`thiserror`
- **被引用方**: 被本 crate 的所有模块引用

## 实现备注

- `Result<T>` 类型别名定义在此模块中。
- Starlark 错误的位置信息通过 `span().resolve_span()` 提取，行号和列号转换为从 1 开始的索引。
