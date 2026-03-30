# `errors.rs` — 统一执行模块的错误类型定义

## 功能概述

定义了 `UnifiedExecError` 枚举，涵盖统一执行模块中所有可能的错误场景，包括进程创建失败、进程运行失败、未知进程 ID、标准输入写入失败、标准输入已关闭、缺少命令行以及沙箱拒绝等。同时提供了便捷的构造方法。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UnifiedExecError` | 枚举 | 统一执行模块的错误类型，包含 `CreateProcess`、`ProcessFailed`、`UnknownProcessId`、`WriteToStdin`、`StdinClosed`、`MissingCommandLine`、`SandboxDenied` 七个变体 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_process` | `fn(message: String) -> Self` | 创建 `CreateProcess` 错误变体 |
| `process_failed` | `fn(message: String) -> Self` | 创建 `ProcessFailed` 错误变体 |
| `sandbox_denied` | `fn(message: String, output: ExecToolCallOutput) -> Self` | 创建 `SandboxDenied` 错误变体，附带原始执行输出 |

## 依赖关系

- **引用的 crate**: `thiserror`（用于派生 `Error` trait）
- **引用的内部模块**: `crate::exec::ExecToolCallOutput`
- **被引用方**: `process.rs`、`process_manager.rs`、`mod.rs` 等统一执行模块的各个文件
