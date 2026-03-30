# `arg_resolver.rs` — 位置参数解析与模式匹配引擎

## 功能概述

负责将实际观察到的命令行位置参数与策略中定义的参数模式进行匹配。采用前缀-变参-后缀的分割策略，将参数模式按基数分为固定基数的前缀模式、一个可选的变参模式和固定基数的后缀模式，然后依次匹配实际参数。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PositionalArg` | 结构体 | 表示一个位置参数，包含 `index` (在原始参数列表中的位置) 和 `value` (参数值) |
| `ParitionedArgs` | 结构体 (内部) | 分割后的参数模式容器，包含前缀模式、后缀模式、变参模式及其各自需要的参数数量 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `resolve_observed_args_with_patterns` | `(program, args, arg_patterns) -> Result<Vec<MatchedArg>>` | 核心函数：将实际参数与模式列表进行匹配，返回匹配后的参数列表 |
| `partition_args` | `(program, arg_patterns) -> Result<ParitionedArgs>` | 将模式列表分割为前缀/变参/后缀三部分，限制最多只能有一个变参模式 |
| `get_range_checked` | `(vec, range) -> Result<&[T]>` | 安全的范围切片访问，避免越界 |

## 依赖关系

- **引用的 crate**: `serde`
- **被引用方**: `program.rs` (在 `ProgramSpec::check` 中调用)

## 实现备注

- 采用朴素匹配实现：先匹配前缀固定参数，再计算变参需要消耗的参数数量，最后匹配后缀固定参数。
- 多余的未匹配参数会返回 `Error::UnexpectedArguments` 错误。
