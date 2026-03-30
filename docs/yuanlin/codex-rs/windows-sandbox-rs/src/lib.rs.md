# `lib.rs` -- Windows 沙箱 crate 入口与跨平台适配

## 功能概述

这是 `codex-windows-sandbox-rs` crate 的入口文件。它使用条件编译将所有 Windows 特有模块（acl、audit、cap、desktop、dpapi、env、firewall 等共 20+ 个模块）门控在 `target_os = "windows"` 下，并在非 Windows 平台提供返回错误的 stub 实现。crate 的核心功能是通过 Windows 受限令牌（Restricted Token）+ ACL 权限控制 + 防火墙规则实现进程级沙箱。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CaptureResult` | struct | 沙箱执行结果：退出码、stdout、stderr、是否超时 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_windows_sandbox_capture` | `pub fn run_windows_sandbox_capture(policy, cwd, codex_home, command, cwd, env, timeout, desktop) -> Result<CaptureResult>` | Windows 沙箱执行入口：创建受限令牌、设置 ACL、启动受限进程 |
| `run_windows_sandbox_legacy_preflight` | `pub fn run_windows_sandbox_legacy_preflight(policy, cwd, codex_home, cwd, env) -> Result<()>` | 旧版预检：仅设置 ACL 而不启动进程 |
| `run_elevated_setup` | `pub fn run_elevated_setup(request, overrides) -> Result<()>` | 通过 UAC 提权执行沙箱初始设置 |

## 依赖关系

- **引用的 crate**: `windows_sys`, `anyhow`, `serde`, `codex_protocol`
- **被引用方**: `codex-core` 中的 Windows 沙箱执行路径

## 实现备注

- Windows 沙箱实现基于三层防护：1) 受限令牌限制进程能力 2) ACL 控制文件系统访问 3) 防火墙规则控制网络访问
- `windows_impl` 模块包含完整的沙箱执行流程：创建 stdio 管道、以受限令牌启动进程、读取 stdout/stderr、等待完成
- stub 模块确保非 Windows 平台编译通过但调用时返回错误
- `#![allow(unsafe_op_in_unsafe_fn)]` 暂时抑制 Rust 2024 edition 的 unsafe 块内操作警告
