# `oauth.rs` -- MCP OAuth 凭据管理

## 功能概述
管理 MCP OAuth 凭据的完整生命周期：加载、保存、删除和刷新。支持三种存储模式（Auto/File/Keyring），Auto 模式优先使用 OS 级密钥环（macOS Keychain / Windows Credential Manager / Linux Secret Service），不可用时回退到 `CODEX_HOME/.credentials.json` 文件。包含 `OAuthPersistor` 用于在运行时自动持久化和按需刷新令牌。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `StoredOAuthTokens` | struct | 持久化的 OAuth 令牌，包含 server_name、url、client_id、token_response 和 expires_at |
| `OAuthCredentialsStoreMode` | enum | 凭据存储模式：Auto、File、Keyring |
| `WrappedOAuthTokenResponse` | struct | 包装 OAuthTokenResponse 以支持 PartialEq |
| `OAuthPersistor` | struct | 运行时 OAuth 持久化器，监控令牌变更并自动保存/刷新 |
| `FallbackTokenEntry` | struct | 文件回退存储的令牌条目格式 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_oauth_tokens` | `fn(server_name, url, store_mode) -> Result<Option<StoredOAuthTokens>>` | 按存储模式加载 OAuth 令牌 |
| `save_oauth_tokens` | `fn(server_name, tokens, store_mode) -> Result<()>` | 按存储模式保存 OAuth 令牌 |
| `delete_oauth_tokens` | `fn(server_name, url, store_mode) -> Result<bool>` | 删除所有存储位置的 OAuth 令牌 |
| `OAuthPersistor::persist_if_needed` | `async fn(&self) -> Result<()>` | 仅在令牌变化时持久化 |
| `OAuthPersistor::refresh_if_needed` | `async fn(&self) -> Result<()>` | 在令牌即将过期时刷新 |
| `compute_expires_at_millis` | `fn(response) -> Option<u64>` | 将 expires_in 转换为绝对过期时间戳 |

## 依赖关系
- **引用的 crate**: `oauth2`, `rmcp`, `codex_keyring_store`, `sha2`, `serde_json`, `codex_utils_home_dir`
- **被引用方**: `rmcp_client.rs`, `perform_oauth_login.rs`, `auth_status.rs`

## 实现备注
- 存储键由 server_name 和 URL 的 SHA-256 前 16 字符组成
- 刷新偏移量为 30 秒（`REFRESH_SKEW_MILLIS`），在令牌过期前 30 秒触发刷新
- 文件存储时设置 Unix 权限 0o600
- 成功保存到密钥环后会删除文件回退中的旧条目
