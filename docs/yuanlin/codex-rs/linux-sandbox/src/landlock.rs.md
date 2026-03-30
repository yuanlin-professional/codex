# `landlock.rs` -- Linux 进程内沙箱原语（seccomp + Landlock）

## 功能概述

本模块实现了 Linux 沙箱的进程内安全限制：`PR_SET_NO_NEW_PRIVS` 标志和 seccomp BPF 网络过滤器。文件系统限制现由 bubblewrap 负责，Landlock 文件系统规则作为旧版备用方案保留。seccomp 过滤器支持两种网络模式：完全限制模式（仅允许 AF_UNIX socket）和代理路由模式（仅允许 AF_INET/AF_INET6，阻止 AF_UNIX）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `NetworkSeccompMode` | enum | 网络 seccomp 模式：`Restricted`（完全限制）或 `ProxyRouted`（代理路由） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `apply_sandbox_policy_to_current_thread` | `pub fn apply_sandbox_policy_to_current_thread(...) -> Result<()>` | 在当前线程上应用所有沙箱限制（no_new_privs + seccomp + 可选 landlock） |
| `set_no_new_privs` | `fn set_no_new_privs() -> Result<()>` | 通过 `prctl(PR_SET_NO_NEW_PRIVS)` 设置安全位 |
| `install_network_seccomp_filter_on_current_thread` | `fn install_network_seccomp_filter_on_current_thread(mode) -> Result<(), SandboxErr>` | 安装 seccomp BPF 网络过滤器 |
| `install_filesystem_landlock_rules_on_current_thread` | `fn install_filesystem_landlock_rules_on_current_thread(writable_roots) -> Result<()>` | 安装 Landlock 文件系统规则（旧版备用） |
| `network_seccomp_mode` | `fn network_seccomp_mode(...) -> Option<NetworkSeccompMode>` | 根据策略和代理设置决定 seccomp 模式 |

## 依赖关系

- **引用的 crate**: `landlock`, `seccompiler`, `libc`, `codex_core`, `codex_protocol`
- **被引用方**: `linux_run_main.rs`

## 实现备注

- Restricted 模式阻止几乎所有网络相关系统调用（connect, bind, listen 等），仅允许 AF_UNIX 的 socket/socketpair
- ProxyRouted 模式相反：允许 AF_INET/AF_INET6（用于到达本地 TCP 桥接），但阻止 AF_UNIX（防止绕过代理桥接）
- 还阻止 `ptrace` 和 `io_uring` 相关系统调用以防止 seccomp 绕过
- `recvfrom` 被有意允许以支持 `cargo clippy` 等工具的 socketpair 子进程管理
