# `cli.rs` — TUI 命令行参数定义

## 功能概述

本文件使用 `clap` 定义了 Codex TUI 的命令行参数结构 `Cli`。它包含用户可见的参数（prompt、model、sandbox 模式、approval 策略等）和内部参数（resume/fork 相关的 `#[clap(skip)]` 字段），后者由顶层子命令包装器设置。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Cli` | 结构体 | 命令行参数集合，包含 prompt、images、model、sandbox_mode、approval_policy 等字段 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| - | - | 本文件仅定义数据结构，无独立方法 |

## 依赖关系

- **引用的 crate**: `clap`（命令行解析）、`codex_utils_cli`（ApprovalModeCliArg、CliConfigOverrides）
- **被引用方**: `lib.rs`（`run_main` 接收 Cli 参数并驱动应用启动流程）

## 实现备注

- `--full-auto` 是 `--ask-for-approval on-request --sandbox workspace-write` 的便捷别名。
- `--dangerously-bypass-approvals-and-sandbox`（别名 `--yolo`）跳过所有确认和沙箱，与 `approval_policy`/`full_auto` 互斥。
- `--no-alt-screen` 禁用备用屏幕模式，适用于 Zellij 等严格遵循 xterm 规范的终端复用器。
- Resume/fork 相关字段使用 `#[clap(skip)]` 标记，不暴露为用户可见参数。
