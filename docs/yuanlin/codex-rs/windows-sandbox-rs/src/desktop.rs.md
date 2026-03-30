# `desktop.rs` -- Windows 私有桌面管理

## 功能概述

该文件实现了 Windows 沙箱的私有桌面（Private Desktop）创建与管理功能。当启用私有桌面时，通过 `CreateDesktopW` API 创建一个随机命名的独立桌面对象，并通过修改 DACL 授予当前登录 SID 完全访问权限。私有桌面用于隔离沙箱进程的 UI 环境，防止其与主桌面交互。不启用时默认使用 `Winsta0\Default` 桌面。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `LaunchDesktop` | struct | 启动桌面封装，持有私有桌面实例（可选）和 startup name 宽字符串 |
| `PrivateDesktop` | struct | 私有桌面实例，持有桌面句柄和随机生成的名称 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `LaunchDesktop::prepare` | `fn(use_private_desktop: bool, logs_base_dir: Option<&Path>) -> Result<Self>` | 根据配置准备启动桌面（私有或默认） |
| `LaunchDesktop::startup_info_desktop` | `fn(&self) -> *mut u16` | 返回可直接设置到 STARTUPINFOW.lpDesktop 的宽字符串指针 |
| `PrivateDesktop::create` | `fn(logs_base_dir: Option<&Path>) -> Result<Self>` | 创建私有桌面并授予访问权限 |
| `grant_desktop_access` | `unsafe fn(handle, logs_base_dir) -> Result<()>` | 通过 SetEntriesInAclW + SetSecurityInfo 授予登录 SID 桌面完全访问 |

## 依赖关系

- **引用的 crate**: `windows_sys`（StationsAndDesktops、Security API）, `rand`, `crate::token`, `crate::winutil`, `crate::logging`
- **被引用方**: `process.rs` 中的 `create_process_as_user`

## 实现备注

- 私有桌面名称使用 128 位随机数确保唯一性：`CodexSandboxDesktop-{hex}`
- `DESKTOP_ALL_ACCESS` 手动组合所有桌面权限位
- `PrivateDesktop` 实现 `Drop` trait，析构时自动调用 `CloseDesktop`
- 桌面 DACL 修改使用 `SE_WINDOW_OBJECT` 安全对象类型
