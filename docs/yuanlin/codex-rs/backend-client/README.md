# backend-client

## 功能概述

`codex-backend-client` 是 Codex 项目中用于与 Codex 后端 REST API 交互的 HTTP 客户端库。它封装了所有与 Codex 云端服务（包括 Codex API 和 ChatGPT backend-api 两种路径风格）的通信逻辑，提供类型安全的请求/响应接口。

主要功能包括：

- **速率限制查询**：获取用户的速率限制快照，支持主要窗口和次要窗口，以及额外的速率限制维度。
- **任务管理**：列出任务（带分页/过滤）、获取任务详情、创建新任务。
- **配置获取**：从后端拉取托管的 requirements 配置文件。
- **兄弟回合查询**：获取某个任务回合的兄弟回合列表。
- **双路径风格**：自动检测并支持 `/api/codex/...` 和 `/wham/...`（ChatGPT backend-api）两种 URL 路径风格。
- **认证管理**：支持 Bearer Token 认证和 ChatGPT Account ID 头。
- **自定义 CA 证书**：通过 `codex-client` 支持自定义 CA 证书的 reqwest 客户端。

## 架构说明

该 crate 采用简洁的客户端 + 类型模块设计：

```
Client (reqwest HTTP 客户端)
  |
  ├── PathStyle::CodexApi     -> /api/codex/...
  └── PathStyle::ChatGptApi   -> /wham/...
          |
          v
     Codex Backend REST API
```

- **Client**：核心 HTTP 客户端，内部持有 `reqwest::Client`、认证 token、用户代理和路径风格配置。支持 builder 模式配置。
- **PathStyle**：根据 base_url 自动推断 API 路径前缀。包含 `/backend-api` 的 URL 自动使用 ChatGPT 路径风格。
- **类型系统**：请求/响应类型分为两层：
  - `codex-backend-openapi-models` 提供从 OpenAPI 规范自动生成的底层模型。
  - `backend-client/src/types.rs` 提供手工优化的上层类型（如 `CodeTaskDetailsResponse`），处理复杂的嵌套结构和可选字段。

错误处理通过 `RequestError` 枚举区分 HTTP 状态码错误和其他错误，支持检查 401 未授权状态。

## 目录结构

```
backend-client/
├── Cargo.toml
├── src/
│   ├── lib.rs       # 模块声明和公共导出
│   ├── client.rs    # Client 结构体：HTTP 请求封装、认证、路径风格
│   └── types.rs     # 手工类型定义：CodeTaskDetailsResponse、Turn、
│                    #   TurnItem、ContentFragment 等
└── tests/
    └── fixtures/    # 测试 JSON fixture 文件
        ├── task_details_with_diff.json
        └── task_details_with_error.json
```

## 依赖关系

| 依赖 | 说明 |
|------|------|
| `codex-backend-openapi-models` | OpenAPI 自动生成的后端模型 |
| `codex-client` | 自定义 CA 的 reqwest 客户端构建器 |
| `codex-protocol` | 协议公共类型（RateLimitSnapshot、PlanType 等） |
| `codex-core` | 核心功能（CodexAuth 认证） |
| `reqwest` | HTTP 客户端（rustls-tls） |
| `anyhow` | 错误处理 |
| `serde` / `serde_json` | 序列化/反序列化 |

## 核心接口/API

### Client

```rust
// 创建客户端
pub fn new(base_url: impl Into<String>) -> Result<Self>;
pub fn from_auth(base_url: impl Into<String>, auth: &CodexAuth) -> Result<Self>;

// Builder 方法
pub fn with_bearer_token(self, token: impl Into<String>) -> Self;
pub fn with_user_agent(self, ua: impl Into<String>) -> Self;
pub fn with_chatgpt_account_id(self, account_id: impl Into<String>) -> Self;
pub fn with_path_style(self, style: PathStyle) -> Self;

// 速率限制
pub async fn get_rate_limits(&self) -> Result<RateLimitSnapshot>;
pub async fn get_rate_limits_many(&self) -> Result<Vec<RateLimitSnapshot>>;

// 任务管理
pub async fn list_tasks(&self, limit, task_filter, environment_id, cursor) -> Result<PaginatedListTaskListItem>;
pub async fn get_task_details(&self, task_id: &str) -> Result<CodeTaskDetailsResponse>;
pub async fn create_task(&self, request_body: serde_json::Value) -> Result<String>;

// 兄弟回合
pub async fn list_sibling_turns(&self, task_id, turn_id) -> Result<TurnAttemptsSiblingTurnsResponse>;

// 配置
pub async fn get_config_requirements_file(&self) -> Result<ConfigFileResponse, RequestError>;
```

### RequestError

```rust
pub enum RequestError {
    UnexpectedStatus { method, url, status, content_type, body },
    Other(anyhow::Error),
}

impl RequestError {
    pub fn status(&self) -> Option<StatusCode>;
    pub fn is_unauthorized(&self) -> bool;
}
```

### CodeTaskDetailsResponseExt（扩展 trait）

```rust
pub trait CodeTaskDetailsResponseExt {
    fn unified_diff(&self) -> Option<String>;
    fn assistant_text_messages(&self) -> Vec<String>;
    fn user_text_prompt(&self) -> Option<String>;
    fn assistant_error_message(&self) -> Option<String>;
}
```
