# `linux_run_main.rs` -- Linux 沙箱主逻辑

## 功能概述

本模块是 Linux 沙箱辅助进程的核心逻辑。它解析命令行参数（通过 `clap`），协调 bubblewrap 文件系统隔离和 seccomp 网络限制的两阶段执行流程。外层阶段通过 bubblewrap 构建文件系统视图，内层阶段（通过 `--apply-seccomp-then-exec`）在沙箱环境中应用 `no_new_privs` 和 seccomp 过滤器，然后 exec 进用户命令。还包含旧版 Landlock 回退路径。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `LandlockCommand` | struct (derive Parser) | CLI 参数定义，包含沙箱策略、工作目录、网络模式等选项 |
| `EffectiveSandboxPolicies` | struct | 解析后的有效沙箱策略三元组（旧策略 + 文件系统策略 + 网络策略） |
| `ResolveSandboxPoliciesError` | enum | 策略解析过程中的错误类型（部分策略、不匹配、缺失配置等） |
| `InnerSeccompCommandArgs` | struct | 构建内部 seccomp 命令所需的全部参数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_main` | `pub fn run_main() -> !` | 入口函数：解析参数、决定执行路径（seccomp 内层/bwrap 外层/旧版 landlock） |
| `resolve_sandbox_policies` | `fn resolve_sandbox_policies(...) -> Result<EffectiveSandboxPolicies, ...>` | 统一解析旧策略和 split 策略，确保一致性 |
| `run_bwrap_with_proc_fallback` | `fn run_bwrap_with_proc_fallback(...) -> !` | 运行 bubblewrap，若 `/proc` 挂载失败则自动降级为不挂载 |
| `build_inner_seccomp_command` | `fn build_inner_seccomp_command(args) -> Vec<String>` | 构建内层 seccomp 阶段的命令行参数 |
| `exec_or_panic` | `fn exec_or_panic(command: Vec<String>) -> !` | 通过 `execvp` 系统调用执行目标命令 |
| `preflight_proc_mount_support` | `fn preflight_proc_mount_support(...) -> bool` | 在子进程中预检 `/proc` 挂载是否可用 |

## 依赖关系

- **引用的 crate**: `clap`, `codex_protocol`, `codex_sandboxing`
- **被引用方**: `lib.rs`（通过 `run_main()` 导出）

## 实现备注

- 两阶段设计的原因：bubblewrap 可能依赖 setuid 位，而 `no_new_privs` 会阻止 setuid 提权，因此先运行 bubblewrap 建立文件系统视图，再在内层应用 seccomp
- `/proc` 挂载预检使用 fork + exec 在子进程中尝试，通过 pipe 捕获 stderr 判断是否失败
- split-policy 机制允许文件系统策略和网络策略独立指定，支持比旧策略更精细的控制
