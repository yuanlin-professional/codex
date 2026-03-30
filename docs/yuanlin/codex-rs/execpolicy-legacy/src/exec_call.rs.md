# `exec_call.rs` — exec 调用表示

## 功能概述

定义了 `ExecCall` 结构体，表示一次 `execv(3)` 风格的命令调用，包含程序名和参数列表。提供了构造函数和 `Display` trait 实现，用于在日志和错误信息中格式化输出命令。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecCall` | 结构体 | 表示一次命令调用，包含 `program: String` 和 `args: Vec<String>` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `(program: &str, args: &[&str]) -> Self` | 从字符串切片构造 ExecCall 实例 |

## 依赖关系

- **引用的 crate**: `serde`
- **被引用方**: `policy.rs`、`program.rs`、`execv_checker.rs`、`main.rs`
