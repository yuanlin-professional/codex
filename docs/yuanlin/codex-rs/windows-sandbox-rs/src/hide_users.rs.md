# `hide_users.rs` -- 隐藏 Windows 沙箱用户账户

## 功能概述

该文件实现了将 Codex 沙箱创建的 Windows 用户账户从登录界面和文件管理器中隐藏的功能。它通过修改 Windows 注册表的 `Winlogon\SpecialAccounts\UserList` 键将用户设为不可见（DWORD 值为 0），同时将用户配置文件目录设置为 HIDDEN|SYSTEM 属性。这样可以避免沙箱内部用户出现在 Windows 登录屏幕和用户列表中。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `USERLIST_KEY_PATH` | const | 注册表 UserList 路径常量 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `hide_newly_created_users` | `fn(usernames: &[String], log_base: &Path)` | 在 Winlogon 注册表中隐藏新建的沙箱用户列表 |
| `hide_current_user_profile_dir` | `fn(log_base: &Path)` | 隐藏当前沙箱用户的配置文件目录（在 command-runner 中以沙箱用户身份运行） |
| `hide_users_in_winlogon` | `fn(usernames, log_base) -> Result<()>` | 在注册表 UserList 键中为每个用户设置 DWORD 0 值 |
| `create_userlist_key` | `fn() -> Result<HKEY>` | 创建或打开 SpecialAccounts\UserList 注册表键 |
| `hide_directory` | `fn(path: &Path) -> Result<bool>` | 给路径设置 HIDDEN+SYSTEM 文件属性，返回是否发生变化 |

## 依赖关系

- **引用的 crate**: `windows_sys`（注册表和文件属性 API）, `anyhow`
- **被引用方**: 沙箱用户创建流程（`sandbox_users.rs`）和命令运行器（`command_runner_win.rs`）

## 实现备注

- 仅在 `target_os = "windows"` 下编译（`#![cfg(target_os = "windows")]`）
- `hide_current_user_profile_dir` 被设计为在 command-runner 中运行，因为用户首次登录时 Windows 才会创建配置文件目录
- 隐藏操作为尽力（best-effort），失败时仅记录日志不中断执行
