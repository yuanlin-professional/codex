# `mod.rs` — Windows PTY 模块（基于 WezTerm）

## 功能概述

Windows PTY 模块入口，基于 WezTerm 源码（MIT 许可）。定义 `WinChild` 和 `WinChildKiller` 用于 Windows 进程生命周期管理，实现 `portable_pty` 的 `Child` 和 `ChildKiller` trait。包含对 Codex bug #13945 的修复：纠正 `TerminateProcess` 返回值判断逻辑（Win32 非零为成功）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `WinChild` | struct | Windows 子进程，封装进程句柄 |
| `WinChildKiller` | struct | Windows 子进程终止器 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `is_complete` | `fn(&mut self) -> IoResult<Option<ExitStatus>>` | 检查进程是否完成 |
| `do_kill` | `fn(&mut self) -> IoResult<()>` | 终止进程 |
| `wait` | `fn(&mut self) -> IoResult<ExitStatus>` | 等待进程退出 |
| `conpty_supported` | `fn() -> bool` | 检查 ConPTY 是否可用 |

## 依赖关系

- **引用的 crate**: `winapi`, `filedescriptor`, `portable_pty`
- **被引用方**: `pty/src/lib.rs`（Windows 平台）

## 实现备注

- 修复了原始 WezTerm 代码中 `TerminateProcess` 返回值反转的 bug（Codex #13945）。
- `WinChild` 实现了 `std::future::Future` 用于异步等待。
