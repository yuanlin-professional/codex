# responses-api-proxy

## 功能概述

`codex-responses-api-proxy` 是一个最小化的 HTTP 反向代理服务器，专门用于将本地的 OpenAI Responses API 请求安全地转发到上游服务（默认为 `https://api.openai.com/v1/responses`）。它的核心设计目标是隔离 API 密钥，使得调用方无需直接持有敏感凭证。

主要功能包括：

- **安全的密钥隔离**：API 密钥通过 stdin 在启动时读入，使用 `from_static()` + `set_sensitive(true)` 保护，不会出现在日志或调试输出中。
- **严格的请求过滤**：仅允许 `POST /v1/responses`，其他所有请求返回 403 Forbidden。
- **透明的请求转发**：转发所有原始请求头（除 Authorization 和 Host），注入代理自身的认证头和上游 Host 头。
- **流式响应支持**：通过禁用 reqwest 默认超时，支持长时间运行的 SSE 响应流。
- **可配置的上游 URL**：通过 `--upstream-url` 参数自定义转发目标。
- **进程信息写入**：通过 `--server-info` 将监听端口和 PID 写入 JSON 文件，方便父进程发现。
- **HTTP 关闭端点**：可选启用 `GET /shutdown` 端点实现远程关闭。
- **进程加固**：通过 `codex-process-hardening` 在 `main()` 前执行安全加固。

## 架构说明

该代理采用同步多线程模型，基于 `tiny_http` 处理入站请求，`reqwest::blocking` 转发上游：

```
stdin (API key 读入)
    |
    v
tiny_http::Server (127.0.0.1:port)
    |
    v  (每个请求一个线程)
reqwest::blocking::Client --> upstream_url (api.openai.com)
    |
    v
Response 流式回传给调用方
```

启动流程：
1. 从 stdin 读取 `Authorization: Bearer xxx` 头（使用 `zeroize` 保护中间缓冲区）。
2. 绑定本地端口（支持指定或 ephemeral 端口）。
3. 可选写入 server-info JSON 文件。
4. 进入请求循环，每个请求在独立线程中处理。

请求处理流程：
1. 验证 `POST /v1/responses`，否则返回 403。
2. 读取完整请求体。
3. 复制所有请求头（排除 Authorization 和 Host）。
4. 注入敏感的 Authorization 头和上游 Host 头。
5. 转发到上游并流式回传响应。

## 目录结构

```
responses-api-proxy/
├── Cargo.toml
└── src/
    ├── main.rs          # 二进制入口：CLI 解析、进程加固
    ├── lib.rs           # 核心逻辑：Args 定义、run_main()、
    │                    #   请求转发、端口绑定、server-info 写入
    └── read_api_key.rs  # 从 stdin 安全读取 API 密钥
```

## 依赖关系

| 依赖 | 说明 |
|------|------|
| `tiny_http` | 轻量级同步 HTTP 服务器 |
| `reqwest` | HTTP 客户端（blocking + json + rustls-tls） |
| `codex-process-hardening` | 进程安全加固 |
| `clap` | CLI 参数解析 |
| `serde` / `serde_json` | server-info JSON 序列化 |
| `zeroize` | 敏感数据内存清零 |
| `anyhow` | 错误处理 |
| `ctor` | `#[ctor]` 属性用于 pre-main 初始化 |
| `libc` | 系统调用 |

## 核心接口/API

### CLI 参数

```rust
pub struct Args {
    /// 监听端口（默认 ephemeral）
    pub port: Option<u16>,
    /// 写入启动信息的 JSON 文件路径
    pub server_info: Option<PathBuf>,
    /// 启用 GET /shutdown 端点
    pub http_shutdown: bool,
    /// 上游转发 URL（默认 https://api.openai.com/v1/responses）
    pub upstream_url: String,
}
```

### 库入口

```rust
/// 代理主入口
pub fn run_main(args: Args) -> Result<()>;
```

### HTTP 端点

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/v1/responses` | 转发到上游 Responses API |
| GET | `/shutdown` | 关闭代理（需 `--http-shutdown`） |
| 其他 | `*` | 返回 403 Forbidden |

### Server Info 输出格式

```json
{"port": 12345, "pid": 67890}
```
