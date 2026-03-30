# `standalone_executable.rs` -- apply_patch 独立可执行文件的入口逻辑

## 功能概述

本文件实现了 apply_patch 作为独立 CLI 工具运行时的主入口逻辑。它处理命令行参数解析（支持单参数传入 patch 内容或从 stdin 读取），调用核心 `apply_patch` 函数执行补丁应用，并根据执行结果设置进程退出码。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| （无导出结构体） | -- | 本文件仅导出函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `main` | `fn() -> !` | 调用 run_main 并以返回值作为 exit code 退出进程 |
| `run_main` | `fn() -> i32` | 解析参数/stdin，调用 apply_patch，返回 0（成功）、1（执行错误）或 2（用法错误） |

## 依赖关系

- **引用的 crate**: `std::io`（Read、Write）
- **引用的内部模块**: `crate::apply_patch`（核心 patch 应用函数）
- **被引用方**: `main.rs` 调用 `codex_apply_patch::main()`

## 实现备注

- 若未提供命令行参数，则从 stdin 读取 patch 内容；stdin 为空时打印 usage 并返回 exit code 2。
- 严格限制只接受一个参数，多余参数返回 exit code 2。
- 调用 `stdout.flush()` 确保管道场景下输出顺序正确。
