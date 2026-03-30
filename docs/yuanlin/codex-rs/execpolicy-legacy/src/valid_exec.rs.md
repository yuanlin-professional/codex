# `valid_exec.rs` — 经策略验证的命令执行表示

## 功能概述

定义了通过策略检查后的命令执行数据模型。`ValidExec` 表示一个已被策略接受的命令调用，包含分类后的标志、选项和位置参数，以及可选的系统路径。`MatchedArg`、`MatchedOpt`、`MatchedFlag` 分别表示匹配后的位置参数、带值选项和布尔标志，在构造时自动执行类型验证。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ValidExec` | 结构体 | 策略验证通过的命令，包含 program、flags、opts、args 和 system_path |
| `MatchedArg` | 结构体 | 匹配后的位置参数，包含索引、类型和值 |
| `MatchedOpt` | 结构体 | 匹配后的带值选项，包含名称、值和类型 |
| `MatchedFlag` | 结构体 | 匹配后的布尔标志，仅包含名称 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ValidExec::new` | `(program, args, system_path) -> Self` | 构造方法 |
| `ValidExec::might_write_files` | `(&self) -> bool` | 判断命令执行是否可能写入文件 |
| `MatchedArg::new` | `(index, type, value) -> Result<Self>` | 构造并验证参数值 |
| `MatchedOpt::new` | `(name, value, type) -> Result<Self>` | 构造并验证选项值 |

## 依赖关系

- **引用的 crate**: `serde`
- **被引用方**: `program.rs`、`execv_checker.rs`、`main.rs`

## 实现备注

- `system_path` 字段提供了优先使用的系统路径列表（如 `/bin/ls`），以避免 PATH 注入攻击。
- `might_write_files` 通过检查所有选项和参数的类型来判断是否有文件写入风险。
