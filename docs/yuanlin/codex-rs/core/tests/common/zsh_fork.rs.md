# `zsh_fork.rs` — Zsh Fork 模式测试辅助

## 功能概述

提供 Zsh Fork 执行模式集成测试的辅助工具。Zsh Fork 是一种特殊的 shell 执行模式，通过 fork zsh 进程并使用 `EXEC_WRAPPER` 环境变量拦截 execve 系统调用来实现沙箱化命令执行。该模块封装了 zsh 运行时的发现、兼容性检查和测试 Codex 实例的构建。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ZshForkRuntime` | struct | 持有 `zsh_path` 和 `main_execve_wrapper_exe` 路径，通过 `apply_to_config()` 方法将 zsh-fork 相关配置应用到 `Config` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `zsh_fork_runtime` | `fn(test_name: &str) -> Result<Option<ZshForkRuntime>>` | 尝试获取 zsh fork 运行时：查找测试用 zsh 二进制、检查 EXEC_WRAPPER 支持、定位 codex-execve-wrapper 二进制。任一步骤失败则输出跳过信息并返回 `None` |
| `build_zsh_fork_test` | `async fn(server, runtime, approval_policy, sandbox_policy, pre_build_hook) -> Result<TestCodex>` | 使用给定的 zsh 运行时和策略参数构建测试 Codex 实例 |
| `restrictive_workspace_write_policy` | `fn() -> SandboxPolicy` | 返回限制性的工作区写入沙箱策略（禁止网络、排除 tmpdir 和 /tmp） |
| `ZshForkRuntime::apply_to_config` | `fn(&self, config, approval, sandbox)` | 启用 `ShellTool` 和 `ShellZshFork` feature，设置 zsh 路径、execve wrapper 路径和权限策略 |

## 依赖关系

- **引用的 crate**: `codex_core`、`codex_features`、`codex_protocol`、`codex_utils_cargo_bin`
- **被引用方**: 被 zsh-fork 相关的集成测试使用

## 实现备注

- `find_test_zsh_path()` 通过 DotSlash 工具获取共享的 zsh 二进制（位于 `app-server/tests/suite/zsh`）
- `supports_exec_wrapper_intercept()` 通过设置 `EXEC_WRAPPER=/usr/bin/false` 运行 zsh 来检查是否支持 execve 拦截（如果 zsh 忽略 EXEC_WRAPPER 则命令成功，不支持拦截）
