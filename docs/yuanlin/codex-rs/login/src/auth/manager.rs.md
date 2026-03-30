# `manager.rs` — 核心认证管理器

## 功能概述
实现 Codex 认证系统的核心模块，包含 `CodexAuth` 枚举（支持 ApiKey/Chatgpt/ChatgptAuthTokens 三种认证模式）、`AuthManager`（集中式认证状态管理器）和 `UnauthorizedRecovery`（401 恢复状态机）。提供登录/登出、token 刷新（含磁盘重载、OAuth 刷新、外部刷新）、workspace 限制验证等完整功能。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `CodexAuth` | 枚举 | 认证类型：ApiKey/Chatgpt/ChatgptAuthTokens |
| `AuthManager` | 结构体 | 集中式认证管理器，提供线程安全的缓存和刷新 |
| `UnauthorizedRecovery` | 结构体 | 401 恢复状态机：Reload -> RefreshToken -> Done |
| `AuthConfig` | 结构体 | 认证配置：codex_home、存储模式、强制登录方法、workspace |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_auth` | `fn(home, env, mode) -> Result<Option<CodexAuth>>` | 从存储加载认证 |
| `enforce_login_restrictions` | `fn(config) -> io::Result<()>` | 验证登录限制并在不匹配时登出 |
| `AuthManager::auth` | `async fn(&self) -> Option<CodexAuth>` | 获取当前认证（必要时自动刷新） |
| `AuthManager::refresh_token` | `async fn(&self) -> Result<()>` | 带保护的 token 刷新 |

## 依赖关系
- **引用的 crate**: `codex_client`, `codex_protocol`, `reqwest`, `chrono`
- **被引用方**: 几乎所有需要认证的 Codex 组件

## 实现备注
Token 刷新使用 AsyncMutex 防止并发刷新。永久性刷新失败（expired/reused/revoked）会被缓存避免重复网络请求。含 20+ 测试用例。
