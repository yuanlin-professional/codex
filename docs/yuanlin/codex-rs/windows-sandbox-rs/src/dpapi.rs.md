# `dpapi.rs` -- Windows DPAPI 数据保护封装

## 功能概述

该文件封装了 Windows Data Protection API (DPAPI) 的加密和解密功能。使用 `CryptProtectData` 和 `CryptUnprotectData` 对敏感数据（如沙箱用户密码）进行机器级别的加密保护。采用 `CRYPTPROTECT_LOCAL_MACHINE` 标志使提升权限和非提升权限的进程都能解密数据。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| - | - | 本文件无公共结构体定义 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `protect` | `fn(data: &[u8]) -> Result<Vec<u8>>` | 使用 DPAPI 加密数据，返回密文字节 |
| `unprotect` | `fn(blob: &[u8]) -> Result<Vec<u8>>` | 使用 DPAPI 解密数据，返回明文字节 |
| `make_blob` | `fn(data: &[u8]) -> CRYPT_INTEGER_BLOB` | 构造 DPAPI 所需的 BLOB 结构 |

## 依赖关系

- **引用的 crate**: `windows_sys`（Cryptography API）, `anyhow`
- **被引用方**: `identity.rs`（解密密码）, `sandbox_users.rs`（加密密码）

## 实现备注

- 使用 `CRYPTPROTECT_UI_FORBIDDEN` 禁止 UI 提示
- 使用 `CRYPTPROTECT_LOCAL_MACHINE` 实现机器级作用域，使不同权限级别的进程均可访问
- 加密后的输出 BLOB 通过 `LocalFree` 释放
