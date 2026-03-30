# `command_runner.rs` -- Hook 命令的子进程执行器

## 功能概述

本文件负责以子进程方式执行 Hook 命令。它构建 shell 命令、通过 stdin 传入 JSON 输入、设置超时机制，并收集 stdout/stderr 输出和退出码。支持自定义 shell 和默认 shell（Unix 上使用 `$SHELL -lc`，Windows 上使用 `%COMSPEC% /C`）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CommandRunResult` | struct | 命令执行结果，包含 started_at、completed_at、duration_ms、exit_code、stdout、stderr 和 error |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_command` | `async fn(shell, handler, input_json, cwd) -> CommandRunResult` | 执行单个 Hook 命令，处理 spawn 失败、stdin 写入失败和超时 |
| `build_command` | `fn(shell, handler) -> Command` | 根据 CommandShell 配置构建 tokio Command 对象 |
| `default_shell_command` | `fn() -> Command` | 创建默认 shell 命令：Unix 下用 `$SHELL -lc`，Windows 下用 `%COMSPEC% /C` |

## 依赖关系

- **引用的 crate**: `tokio`（异步进程管理和超时）、`chrono`（时间戳）
- **引用的内部模块**: `engine::CommandShell`、`engine::ConfiguredHandler`
- **被引用方**: `engine::dispatcher::execute_handlers`

## 实现备注

- 使用 `kill_on_drop(true)` 确保子进程不会泄漏。
- 超时由 `handler.timeout_sec` 控制，超时后返回包含错误信息的 `CommandRunResult`。
- stdin 写入完成后立即释放（通过 `take()`），避免子进程阻塞等待 EOF。
- 空 shell program 表示使用系统默认 shell。
