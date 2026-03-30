# `winutil.rs` -- Windows 通用工具函数集

## 功能概述

该文件提供一组 Windows 平台通用的工具函数，包括字符串宽字符转换（`to_wide`）、Windows 命令行参数引号处理（`quote_windows_arg`）、Win32 错误码格式化（`format_last_error`）、SID 字节与字符串互转、以及账户名到 SID 的解析。这些函数被沙箱模块中多个文件广泛使用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| - | - | 本文件无结构体定义，仅提供工具函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `to_wide` | `fn<S: AsRef<OsStr>>(s: S) -> Vec<u16>` | 将字符串转换为以 null 结尾的宽字符向量 |
| `quote_windows_arg` | `fn(arg: &str) -> String` | 按 CommandLineToArgvW 规则引用参数（仅 Windows 编译） |
| `format_last_error` | `fn(err: i32) -> String` | 使用 FormatMessageW 将 Win32 错误码转为可读描述 |
| `string_from_sid_bytes` | `fn(sid: &[u8]) -> Result<String, String>` | 将 SID 字节通过 ConvertSidToStringSidW 转为字符串形式 |
| `resolve_sid` | `fn(name: &str) -> Result<Vec<u8>>` | 通过 LookupAccountNameW 将账户名解析为 SID 字节 |

## 依赖关系

- **引用的 crate**: `windows_sys`（Security、Diagnostics API）, `anyhow`
- **被引用方**: `elevated_impl.rs`, `runner_pipe.rs`, `hide_users.rs`, `desktop.rs`, `process.rs`, `acl.rs` 等几乎所有 Windows 沙箱模块

## 实现备注

- `quote_windows_arg` 严格遵循 CRT/CommandLineToArgvW 的反斜杠和引号转义规则
- `resolve_sid` 内置知名 SID 快速映射（Administrators、Users、Everyone、SYSTEM 等）
- `format_last_error` 使用 `FORMAT_MESSAGE_ALLOCATE_BUFFER` 让系统分配消息缓冲区
