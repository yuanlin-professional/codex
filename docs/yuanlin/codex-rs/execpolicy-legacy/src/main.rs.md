# `main.rs` — execpolicy-legacy CLI 入口

## 功能概述

提供命令行接口，允许用户对命令进行策略安全检查。支持两种输入模式：直接传入命令参数 (`check`) 或通过 JSON 对象传入 (`check-json`)。检查结果以 JSON 格式输出，包含四种状态：safe（安全，不写文件）、match（匹配但可能写文件）、forbidden（禁止执行）、unverified（无法验证安全性）。通过不同的退出码区分结果。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Args` | 结构体 | CLI 参数定义，包含 `require_safe` 标志、可选的策略文件路径和子命令 |
| `Command` | 枚举 | 子命令：`Check`（execv 风格）和 `CheckJson`（JSON 输入） |
| `ExecArg` | 结构体 | JSON 输入格式，包含 `program` 和 `args` 字段 |
| `Output` | 枚举 | 输出格式，包含 Safe、Match、Forbidden、Unverified 四种结果 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `main` | `() -> Result<()>` | 解析命令行参数、加载策略、执行检查并输出 JSON 结果 |
| `check_command` | `(policy, exec_arg, check) -> (Output, i32)` | 执行策略检查并根据结果返回输出结构和退出码 |

## 依赖关系

- **引用的 crate**: `clap`、`serde`、`serde_json`、`anyhow`、`env_logger`
- **被引用方**: 作为独立 CLI 工具运行

## 实现备注

- 退出码约定：0 = 成功，12 = 匹配但写文件，13 = 可能安全（无法确认），14 = 禁止执行。
- 当 `--require-safe` 未设置时，所有结果退出码均为 0。
