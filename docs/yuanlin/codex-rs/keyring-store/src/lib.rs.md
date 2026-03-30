# `lib.rs` — Keyring 凭证存储抽象

## 功能概述

提供基于系统 keyring 的凭证存储抽象层。定义了 `KeyringStore` trait 和默认实现 `DefaultKeyringStore`，以及用于测试的 `MockKeyringStore`。支持凭证的加载、保存和删除操作，通过 `keyring` crate 与操作系统密钥链交互。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CredentialStoreError` | enum | 凭证存储错误包装 |
| `KeyringStore` | trait | 凭证存储抽象：load、save、delete |
| `DefaultKeyringStore` | struct | 使用系统 keyring 的默认实现 |
| `MockKeyringStore` | struct | 基于 MockCredential 的测试实现 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load` | `(&self, service, account) -> Result<Option<String>>` | 从 keyring 加载密码 |
| `save` | `(&self, service, account, value) -> Result<()>` | 保存密码到 keyring |
| `delete` | `(&self, service, account) -> Result<bool>` | 从 keyring 删除凭证 |

## 依赖关系

- **引用的 crate**: `keyring`、`tracing`
- **被引用方**: 认证模块中的凭证存储
