# cloud-requirements

## 功能概述

`codex-cloud-requirements` 负责从云端后端获取并缓存 Codex 的配置需求文件（`requirements.toml`）。该 crate 仅适用于 ChatGPT Business（企业 CBP）和 Enterprise 客户，为这些客户提供云端配置管理能力，替代从本地文件系统加载配置的方式。

核心特性：

- **失败关闭策略**：当符合条件的 Business/Enterprise 账户无法加载云端配置时，Codex 将拒绝启动，而非在缺少配置的情况下继续运行
- **带签名的本地缓存**：使用 HMAC-SHA256 签名的 JSON 文件缓存云端配置，防止篡改
- **自动刷新**：后台定期刷新缓存（每 5 分钟），缓存过期时间为 30 分钟
- **重试与认证恢复**：网络请求失败时自动重试（最多 5 次），遇到 401 认证错误时尝试刷新令牌

## 架构说明

```
                    +---------------------------+
                    | cloud_requirements_loader | (公共入口)
                    +-------------+-------------+
                                  |
                                  v
                    +---------------------------+
                    | CloudRequirementsService   |
                    |  - fetch_with_timeout()    |
                    |  - fetch()                 |
                    |  - fetch_with_retries()    |
                    |  - refresh_cache_in_bg()   |
                    +-----+----------+----------+
                          |          |
              +-----------+          +-----------+
              v                                  v
    +---------+----------+            +----------+---------+
    | RequirementsFetcher|            |    本地缓存管理      |
    | (trait)            |            |  - load_cache()     |
    +----+---------------+            |  - save_cache()     |
         |                            |  - HMAC 签签/验签    |
         v                            +--------------------+
    +----+-------------------+
    |BackendRequirements-    |
    |Fetcher                 |
    | (codex-backend-client) |
    +------------------------+

依赖:
    codex-core            (AuthManager, ConfigRequirementsToml, CloudRequirementsLoader)
    codex-backend-client  (后端 API 客户端)
    codex-protocol        (PlanType 等协议类型)
    codex-otel            (遥测指标)
```

## 目录结构

```
cloud-requirements/
  src/
    lib.rs    # 全部实现集中在此文件中
  Cargo.toml
```

该 crate 结构精简，所有逻辑都在 `lib.rs` 单文件中实现（约 2000 行，含测试）。

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-core` | AuthManager、配置加载器类型、backoff 工具 |
| `codex-backend-client` | 后端 API 客户端（获取配置文件） |
| `codex-protocol` | PlanType 等协议类型定义 |
| `codex-otel` | OpenTelemetry 遥测指标上报 |
| `async-trait` | 异步 trait 支持 |
| `tokio` | 异步运行时（文件 I/O、定时器、同步原语） |
| `hmac` / `sha2` | HMAC-SHA256 缓存签名 |
| `base64` | Base64 编解码（签名序列化） |
| `chrono` | 时间处理（缓存过期判断） |
| `serde` / `serde_json` | 缓存文件序列化 |
| `toml` | 解析 requirements.toml 配置 |
| `thiserror` | 错误类型派生 |
| `tracing` | 结构化日志 |

## 核心接口/API

### 公共入口函数

```rust
/// 创建云端配置加载器（需要 AuthManager）
pub fn cloud_requirements_loader(
    auth_manager: Arc<AuthManager>,
    chatgpt_base_url: String,
    codex_home: PathBuf,
) -> CloudRequirementsLoader;

/// 创建云端配置加载器（用于存储层，自动创建 AuthManager）
pub fn cloud_requirements_loader_for_storage(
    codex_home: PathBuf,
    enable_codex_api_key_env: bool,
    credentials_store_mode: AuthCredentialsStoreMode,
    chatgpt_base_url: String,
) -> CloudRequirementsLoader;
```

### 内部核心类型

#### `CloudRequirementsService`

云端配置服务的核心实现：

- `fetch_with_timeout()` -- 带超时的配置获取（15 秒超时）
- `fetch()` -- 完整的配置获取流程（先查缓存，再网络请求）
- `fetch_with_retries()` -- 带重试的网络请求（最多 5 次，含认证恢复）
- `refresh_cache_in_background()` -- 后台缓存刷新循环

#### `RequirementsFetcher` trait

```rust
#[async_trait]
trait RequirementsFetcher: Send + Sync {
    async fn fetch_requirements(
        &self,
        auth: &CodexAuth,
    ) -> Result<Option<String>, FetchAttemptError>;
}
```

#### 缓存结构

```rust
struct CloudRequirementsCacheFile {
    signed_payload: CloudRequirementsCacheSignedPayload,
    signature: String,  // HMAC-SHA256 Base64 签名
}

struct CloudRequirementsCacheSignedPayload {
    cached_at: DateTime<Utc>,
    expires_at: DateTime<Utc>,
    chatgpt_user_id: Option<String>,
    account_id: Option<String>,
    contents: Option<String>,  // requirements.toml 原始内容
}
```

### 关键常量

| 常量 | 值 | 说明 |
|------|-----|------|
| `CLOUD_REQUIREMENTS_TIMEOUT` | 15s | 网络请求超时 |
| `CLOUD_REQUIREMENTS_MAX_ATTEMPTS` | 5 | 最大重试次数 |
| `CLOUD_REQUIREMENTS_CACHE_REFRESH_INTERVAL` | 5min | 后台缓存刷新间隔 |
| `CLOUD_REQUIREMENTS_CACHE_TTL` | 30min | 缓存过期时间 |

### 适用范围

该 crate 仅对以下账户类型生效：
- ChatGPT Business (包括 `business`、`enterprise_cbp_usage_based` 等)
- ChatGPT Enterprise (`enterprise`、`hc`)

对于 API Key 认证或非 Business/Enterprise 计划的用户，`fetch()` 直接返回 `Ok(None)`，不进行任何网络请求。
