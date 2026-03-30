# `lib.rs` — 云端配置需求加载器

## 功能概述

从后端 API 获取企业/商业 ChatGPT 账户的 `requirements.toml` 配置需求。实现了带有 HMAC-SHA256 签名的本地缓存机制（30 分钟 TTL）、自动重试（最多 5 次）、认证恢复、超时控制（15 秒）和后台定期刷新（5 分钟间隔）。对于不符合条件的账户（非 ChatGPT 认证或非 Business/Enterprise 计划），直接返回 None。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CloudRequirementsService` | struct | 核心服务，包含认证管理器、fetcher、缓存路径和超时配置 |
| `CloudRequirementsCacheFile` | struct | 缓存文件结构：签名载荷 + HMAC 签名 |
| `CloudRequirementsCacheSignedPayload` | struct | 签名载荷：缓存时间、过期时间、用户/账户 ID、内容 |
| `FetchAttemptError` | enum | 获取错误：Retryable（可重试）或 Unauthorized（认证失败） |
| `CacheLoadStatus` | enum | 缓存加载状态：各种失败原因（未找到、过期、签名无效、身份不匹配等） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `cloud_requirements_loader` | `(auth_manager, chatgpt_base_url, codex_home) -> CloudRequirementsLoader` | 创建加载器，启动获取和后台刷新任务 |
| `cloud_requirements_loader_for_storage` | `(codex_home, ...) -> CloudRequirementsLoader` | 带存储模式的便捷加载器构造函数 |
| `fetch_with_timeout` | `async (&self) -> Result<Option<ConfigRequirementsToml>>` | 带超时的获取入口 |
| `fetch_with_retries` | `async (&self, auth, trigger) -> Result<...>` | 带重试和认证恢复的获取逻辑 |
| `load_cache` / `save_cache` | `async (...)` | 缓存读写，带 HMAC 签名验证 |

## 依赖关系

- **引用的 crate**: `codex_backend_client`、`codex_core`、`codex_protocol`、`codex_otel`、`hmac`、`sha2`、`chrono`
- **被引用方**: `exec/src/lib.rs`、`cli/src/lib.rs`

## 实现备注

- 缓存签名使用 HMAC-SHA256，支持多个读取密钥以便密钥轮换。
- 对于 Business/Enterprise ChatGPT 用户采用"失败关闭"策略：无法加载需求时中止配置。
- 后台刷新任务存储在全局 OnceLock 中，新任务会 abort 旧任务。
