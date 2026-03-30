# `spawn.rs` — 子进程创建与沙箱环境配置

## 功能概述

此文件负责根据执行参数和沙箱策略创建子进程。它处理环境变量的设置（包括网络代理和沙箱标记）、标准 I/O 的重定向、TTY 分离以及 Linux 上的父进程死亡信号设置。文件确保子进程在 Codex 主进程被终止时能够正确清理。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `StdioPolicy` | 枚举 | I/O 策略：`RedirectForShellTool`（重定向用于 shell 工具）或 `Inherit`（继承父进程） |
| `SpawnChildRequest` | 结构体 | 子进程创建请求，包含程序路径、参数、工作目录、网络策略、I/O 策略和环境变量 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `spawn_child_async` | `async (request: SpawnChildRequest) -> io::Result<Child>` | 异步创建子进程 |

## 依赖关系

- **引用的 crate**: `codex_network_proxy`（`NetworkProxy`）、`codex_protocol`（`NetworkSandboxPolicy`）、`tokio::process`
- **被引用方**: `landlock.rs`、统一执行引擎和其他进程创建路径

## 实现备注

- `CODEX_SANDBOX_NETWORK_DISABLED` 环境变量在网络受限时设为 `"1"`
- `RedirectForShellTool` 模式将 stdin 设为 null（避免 ripgrep 等工具阻塞等待输入）
- Unix 系统上使用 `pre_exec` 钩子分离 TTY 和设置父进程死亡信号
- Linux 上通过 `prctl(2)` 在父进程死亡时向子进程发送 SIGTERM
- `kill_on_drop(true)` 确保子进程在 `Child` 对象析构时被终止
- `env_clear()` + `envs()` 模式确保不会泄漏意外的环境变量
