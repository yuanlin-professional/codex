# `sandboxing/mod.rs` — 沙箱执行请求与命令执行适配层

## 功能概述

该模块是 core 层的沙箱执行适配器，负责将来自 `codex-sandboxing` crate 的沙箱转换命令重新包装为内部的 `ExecRequest`，并提供执行入口。它衔接了策略选择层（`codex-sandboxing`）和底层命令执行层（`crate::exec`），管理沙箱类型、文件系统策略、网络策略、环境变量注入等执行时元数据。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecOptions` | struct | 执行选项，包含 `expiration`（超时策略）和 `capture_policy`（输出捕获策略） |
| `ExecRequest` | struct | 完整的执行请求，包含命令、工作目录、环境变量、网络代理、沙箱类型、各种沙箱策略、Windows 特定配置等 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ExecRequest::new` | `fn(command, cwd, env, ...) -> Self` | 直接构造完整的 `ExecRequest` |
| `ExecRequest::from_sandbox_exec_request` | `fn(request: SandboxExecRequest, options: ExecOptions) -> Self` | 从 `codex-sandboxing` 的 `SandboxExecRequest` 转换，自动注入网络禁用和 seatbelt 环境变量 |
| `execute_env` | `async fn(exec_request, stdout_stream) -> Result<ExecToolCallOutput>` | 执行沙箱命令的简便入口 |
| `execute_exec_request_with_after_spawn` | `async fn(exec_request, stdout_stream, after_spawn) -> Result<ExecToolCallOutput>` | 带 after_spawn 回调的执行入口 |

## 依赖关系

- **引用的 crate**: `codex_sandboxing`（`SandboxExecRequest`、`SandboxType`）、`codex_protocol`（`SandboxPolicy`、`FileSystemSandboxPolicy`、`NetworkSandboxPolicy`、`SandboxPermissions`、`WindowsSandboxLevel`）、`codex_network_proxy::NetworkProxy`
- **引用的模块**: `crate::exec`（`execute_exec_request`、`ExecCapturePolicy`、`ExecExpiration`、`ExecToolCallOutput`、`StdoutStream`）、`crate::spawn`（环境变量常量）
- **被引用方**: 命令执行流程中需要沙箱隔离的所有调用路径

## 实现备注

在 `from_sandbox_exec_request` 转换过程中，自动处理了两个环境变量注入：当网络沙箱策略未启用时设置 `CODEX_SANDBOX_NETWORK_DISABLED_ENV_VAR`；在 macOS 平台上使用 Seatbelt 沙箱时设置 `CODEX_SANDBOX_ENV_VAR`。`windows_restricted_token_filesystem_overlay` 字段在构造时始终初始化为 `None`，由后续流程按需设置。
