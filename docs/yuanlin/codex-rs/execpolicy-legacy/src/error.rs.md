# `error.rs` — 错误类型定义

## 功能概述

定义了 `execpolicy-legacy` crate 中所有可能的错误类型枚举 `Error`。覆盖了策略检查过程中可能遇到的各种错误场景，包括程序无策略规范、选项缺失值、未知选项、参数不足、多重变参模式冲突、文件路径校验失败等。所有错误类型都支持序列化以便于 JSON 输出。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Error` | 枚举 | 包含 NoSpecForProgram、OptionMissingValue、UnknownOption、UnexpectedArguments、MultipleVarargPatterns、NotEnoughArgs、EmptyFileName、LiteralValueDidNotMatch、InvalidPositiveInteger、SedCommandNotProvablySafe、ReadablePathNotInReadableFolders、WriteablePathNotInWriteableFolders、CannotCanonicalizePath 等 20+ 种错误变体 |

## 依赖关系

- **引用的 crate**: `serde`、`serde_with`
- **被引用方**: 被本 crate 中几乎所有模块引用

## 实现备注

- 使用 `#[serde(tag = "type")]` 进行标记式序列化，方便在 JSON 输出中区分错误类型。
- `Result<T>` 类型别名定义在此模块中，全 crate 使用。
