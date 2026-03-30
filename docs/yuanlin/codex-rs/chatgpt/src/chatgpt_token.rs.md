# `chatgpt_token.rs` — ChatGPT 令牌全局缓存管理

## 功能概述
使用全局 `LazyLock<RwLock<Option<TokenData>>>` 缓存 ChatGPT 认证令牌数据。提供读取、设置和从 auth.json 初始化令牌的函数，确保整个进程共享统一的令牌快照。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `get_chatgpt_token_data` | `fn() -> Option<TokenData>` | 读取全局缓存的令牌数据 |
| `set_chatgpt_token_data` | `fn(value: TokenData)` | 设置全局令牌缓存 |
| `init_chatgpt_token_from_auth` | `async fn(codex_home, mode) -> io::Result<()>` | 从 auth.json 加载并初始化令牌 |

## 依赖关系
- **引用的 crate**: `codex_core`, `codex_login`
- **被引用方**: `chatgpt_client.rs`, `connectors.rs`
