# `landlock.rs` — Linux 沙箱（bubblewrap/seccomp）命令生成

## 功能概述

此文件负责在 Linux 平台上通过 `codex-linux-sandbox` 辅助程序（基于 bubblewrap + seccomp）启动沙箱化的 shell 工具命令。它将沙箱策略（文件系统和网络）转换为辅助程序的命令行参数，并处理 argv0 的正确设置以确保辅助程序的入口点分发正常工作。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| （无独立类型定义） | — | — |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `spawn_command_under_linux_sandbox` | `async (exe, command, cwd, sandbox_policy, ...) -> io::Result<Child>` | 在 Linux 沙箱下启动命令 |

## 依赖关系

- **引用的 crate**: `codex_sandboxing::landlock`（沙箱命令参数生成）、`codex_network_proxy`、`codex_protocol`、`crate::spawn`
- **被引用方**: `lib.rs` 公开导出此模块，Linux 平台的工具执行使用

## 实现备注

- 从传统的 `SandboxPolicy` 提取 `FileSystemSandboxPolicy` 和 `NetworkSandboxPolicy`
- argv0 处理：如果可执行文件名已是 `CODEX_LINUX_SANDBOX_ARG0`，保留完整路径作为 argv0；否则强制设置为 sandbox 入口点名称
- 网络代理设置 `allow_network_for_proxy(false)` 表示不强制使用托管网络代理
- 最终委托给 `spawn_child_async` 执行实际的进程创建
