# rmcp-client

## 功能概述

`codex-rmcp-client` 是 Codex 项目中的 MCP (Model Context Protocol) 客户端实现，基于 `rmcp` 库构建。它负责连接和管理 MCP 服务器，支持多种传输方式，并集成了 OAuth 认证、工具调用、资源读取和 elicitation（交互式输入请求）等功能。

主要功能包括：

- **多传输方式支持**：
  - **子进程 stdio**：通过 `TokioChildProcess` 启动 MCP 服务器子进程。
  - **Streamable HTTP**：通过 HTTP SSE 流连接远程 MCP 服务器。
- **OAuth 认证流程**：
  - 自动检测服务器是否需要 OAuth 认证。
  - 支持浏览器回调和静默刷新两种 OAuth 登录方式。
  - 通过 `keyring` 安全存储 OAuth 令牌（跨平台：macOS Keychain、Linux Secret Service、Windows Credential Manager）。
- **工具管理**：列出可用工具（带 connector ID 标记）、调用工具。
- **资源管理**：列出资源、读取资源、列出资源模板。
- **Elicitation 支持**：处理服务器发起的交互式输入请求，将其转发给调用方并回传响应。
- **服务器发现**：通过 `which` 解析 MCP 服务器可执行文件路径，支持 PATH 搜索和 npx 等包管理器解析。
- **自定义 HTTP 头**：支持为 Streamable HTTP 连接配置额外请求头。

## 架构说明

```
RmcpClient
  |
  ├── connect_stdio(command, args, env)
  |     |
  |     v
  |   TokioChildProcess --> MCP Server (子进程)
  |
  └── connect_streamable_http(url, headers, oauth_store)
        |
        v
      StreamableHttpClientTransport
        |
        ├── AuthClient (OAuth 令牌注入)
        └── SSE Stream <-- MCP Server (远程 HTTP)
```

核心组件：

- **RmcpClient**：主客户端结构，封装 `RunningService<RoleClient>` 会话。提供静态方法连接服务器，实例方法调用工具和资源。
- **LoggingClientHandler**：MCP 客户端事件处理器，负责日志记录和 elicitation 回调转发。
- **OAuth 模块**：
  - `oauth.rs` -- 令牌存储/加载/删除，支持 keyring 和内存两种模式。
  - `perform_oauth_login.rs` -- 完整 OAuth 登录流程，包括授权码交换和令牌刷新。
- **auth_status.rs**：OAuth 发现和认证状态检测。
- **program_resolver.rs**：MCP 服务器可执行文件路径解析。

## 目录结构

```
rmcp-client/
├── Cargo.toml
└── src/
    ├── lib.rs                    # 模块声明和公共导出
    ├── rmcp_client.rs            # RmcpClient 核心实现：
    │                             #   连接方式、工具调用、资源读取
    ├── logging_client_handler.rs # MCP 事件处理和 elicitation 回调
    ├── oauth.rs                  # OAuth 令牌持久化（keyring/内存）
    ├── perform_oauth_login.rs    # OAuth 登录流程实现
    ├── auth_status.rs            # OAuth 发现和认证状态检测
    ├── program_resolver.rs       # 服务器可执行文件路径解析
    ├── utils.rs                  # HTTP 头构建、环境变量处理等工具函数
    └── bin/
        ├── rmcp_test_server.rs           # 测试用 MCP 服务器
        ├── test_stdio_server.rs          # stdio 传输测试服务器
        └── test_streamable_http_server.rs # HTTP 传输测试服务器
```

## 依赖关系

| 依赖 | 说明 |
|------|------|
| `rmcp` | MCP 协议核心库（client + server + 多种传输） |
| `codex-client` | 自定义 CA 的 reqwest 客户端 |
| `codex-protocol` | 协议公共类型（McpAuthStatus） |
| `codex-keyring-store` | Keyring 存储抽象 |
| `codex-utils-pty` | PTY 工具 |
| `codex-utils-home-dir` | 主目录解析 |
| `reqwest` | HTTP 客户端（json + stream + rustls-tls） |
| `oauth2` | OAuth 2.0 客户端 |
| `keyring` | 跨平台密钥存储 |
| `axum` | HTTP 服务器（OAuth 回调） |
| `tokio` | 异步运行时 |
| `futures` | 异步流处理 |
| `schemars` | JSON Schema 生成 |
| `serde` / `serde_json` | 序列化 |
| `sha2` | 哈希计算 |
| `sse-stream` | SSE 流解析 |
| `tiny_http` | 轻量 HTTP 服务器 |
| `thiserror` | 错误类型派生 |
| `webbrowser` | 打开浏览器（OAuth 登录） |
| `which` | 可执行文件查找 |
| `urlencoding` | URL 编码 |

## 核心接口/API

### RmcpClient

```rust
// 通过 stdio 子进程连接
pub async fn connect_stdio(
    command: &str,
    args: &[String],
    env: HashMap<String, String>,
    send_elicitation: Option<SendElicitation>,
) -> Result<Self>;

// 通过 Streamable HTTP 连接
pub async fn connect_streamable_http(
    url: &str,
    extra_headers: HashMap<String, String>,
    oauth_store_mode: OAuthCredentialsStoreMode,
    send_elicitation: Option<SendElicitation>,
) -> Result<Self>;

// 列出工具（带 connector ID）
pub async fn list_tools(&self) -> Result<ListToolsWithConnectorIdResult>;

// 调用工具
pub async fn call_tool(&self, params: CallToolRequestParams) -> Result<CallToolResult>;

// 列出资源
pub async fn list_resources(&self, cursor: Option<String>) -> Result<ListResourcesResult>;

// 读取资源
pub async fn read_resource(&self, uri: &str) -> Result<ReadResourceResult>;

// 列出资源模板
pub async fn list_resource_templates(&self, cursor: Option<String>) -> Result<ListResourceTemplatesResult>;
```

### OAuth 相关

```rust
// OAuth 认证状态检测
pub async fn determine_streamable_http_auth_status(url: &str) -> Result<McpAuthStatus>;

// OAuth 发现
pub async fn discover_streamable_http_oauth(url: &str) -> Result<Option<StreamableHttpOAuthDiscovery>>;

// OAuth 登录
pub async fn perform_oauth_login(url: &str, store_mode: OAuthCredentialsStoreMode) -> Result<OauthLoginHandle>;

// 令牌管理
pub fn save_oauth_tokens(server_url: &str, tokens: &StoredOAuthTokens, mode: OAuthCredentialsStoreMode) -> Result<()>;
pub fn delete_oauth_tokens(server_url: &str, mode: OAuthCredentialsStoreMode) -> Result<()>;
```

### Elicitation

```rust
pub struct Elicitation {
    pub message: String,
    pub schema: Value,
    pub request_id: RequestId,
}

pub struct ElicitationResponse {
    pub action: ElicitationAction,
    pub content: Option<HashMap<String, Value>>,
}

pub type SendElicitation = Arc<dyn Fn(Elicitation) -> BoxFuture<'static, Option<ElicitationResponse>> + Send + Sync>;
```
