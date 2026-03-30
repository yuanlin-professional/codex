# `storage.rs` — 认证凭据存储后端

## 功能概述
实现多种认证凭据存储后端的抽象和具体实现。定义 `AuthStorageBackend` trait 和 `AuthCredentialsStoreMode` 枚举。提供四种存储实现：文件存储（auth.json）、Keyring 存储（系统密钥链）、Auto 存储（keyring 优先，文件回退）和 Ephemeral 存储（进程内内存）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `AuthCredentialsStoreMode` | 枚举 | 存储模式：File/Keyring/Auto/Ephemeral |
| `AuthDotJson` | 结构体 | auth.json 结构：auth_mode, openai_api_key, tokens, last_refresh |
| `FileAuthStorage` | 结构体 | 文件存储后端 |
| `KeyringAuthStorage` | 结构体 | Keyring 存储后端 |
| `AutoAuthStorage` | 结构体 | 自动选择存储后端 |
| `EphemeralAuthStorage` | 结构体 | 进程内内存存储后端 |

## 依赖关系
- **引用的 crate**: `codex_keyring_store`, `sha2`, `serde`, `chrono`
- **被引用方**: `manager.rs`

## 实现备注
文件存储使用 0o600 权限。Keyring key 基于 codex_home 路径的 SHA256 前 16 位。含 15+ 测试用例覆盖各存储后端的 CRUD 和错误回退行为。
