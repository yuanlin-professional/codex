# keyring-store

## 功能概述

`codex-keyring-store` 是 Codex 项目的系统钥匙串（Keyring）访问抽象层，提供跨平台的凭证存储接口。它封装了 `keyring` crate，为 macOS（Keychain）、Linux（Secret Service / 持久化本地存储）、Windows（Credential Manager）和 BSD（Secret Service）提供统一的密码/凭证读写能力。该模块主要被 `codex-secrets` 用于保护密钥加密所需的主密钥。

## 架构说明

该模块采用 trait 抽象模式：

1. **KeyringStore trait** - 核心接口抽象，定义了 `load/save/delete` 三个操作。要求实现者满足 `Debug + Send + Sync`。
2. **DefaultKeyringStore** - 默认实现，直接使用 `keyring::Entry` 与系统钥匙串交互。
3. **MockKeyringStore**（测试用）- 基于内存 HashMap 的模拟实现，使用 `keyring::mock::MockCredential` 进行测试。

### 平台适配

| 平台 | Keyring Feature |
|------|-----------------|
| macOS | `apple-native`（Keychain） |
| Linux | `linux-native-async-persistent`（本地持久化） |
| Windows | `windows-native`（Credential Manager） |
| FreeBSD / OpenBSD | `sync-secret-service`（D-Bus Secret Service） |
| 通用 | `crypto-rust`（纯 Rust 加密后端） |

### 错误处理

`CredentialStoreError` 封装了底层 `keyring::Error`，对 `NoEntry`（条目不存在）进行了特殊处理，在 `load` 和 `delete` 操作中不视为错误而是返回 `None` / `false`。

## 目录结构

```
keyring-store/
  Cargo.toml
  src/
    lib.rs   # 完整实现：KeyringStore trait、DefaultKeyringStore、MockKeyringStore
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `keyring` | 系统钥匙串操作（含 `crypto-rust` 及各平台原生 feature） |
| `tracing` | 操作日志追踪 |

## 核心接口/API

### KeyringStore trait

```rust
pub trait KeyringStore: Debug + Send + Sync {
    fn load(&self, service: &str, account: &str) -> Result<Option<String>, CredentialStoreError>;
    fn save(&self, service: &str, account: &str, value: &str) -> Result<(), CredentialStoreError>;
    fn delete(&self, service: &str, account: &str) -> Result<bool, CredentialStoreError>;
}
```

- `load` - 从钥匙串读取密码，不存在时返回 `Ok(None)`
- `save` - 向钥匙串保存密码
- `delete` - 从钥匙串删除凭证，不存在时返回 `Ok(false)`

### 导出类型

- **`DefaultKeyringStore`** - 使用系统钥匙串的默认实现
- **`CredentialStoreError`** - 错误类型
- **`tests::MockKeyringStore`** - 测试用模拟实现（公开在 `tests` 模块中）
