# ollama

## 功能概述

`codex-ollama` 是 Codex 项目中与本地 Ollama 推理服务器进行集成的 crate。它提供:

- **服务器连接与版本验证**: 探测本地 Ollama 服务是否可达,检查版本是否满足 Responses API 的最低要求(>= 0.13.4)。
- **模型管理**: 查询本地已安装模型,自动拉取缺失模型并提供流式进度报告。
- **OpenAI 兼容模式**: 自动检测 base URL 是否使用 OpenAI 兼容端点(`/v1/...`),并切换到对应的探测路径。
- **进度报告系统**: 提供 `PullProgressReporter` trait 和 CLI/TUI 两种报告器实现。

默认 OSS 模型为 `gpt-oss:20b`。

## 架构说明

该 crate 由以下模块组成:

1. **lib.rs** -- 公开入口和高层函数:
   - `ensure_oss_ready()`: 验证 Ollama 可达性,检查模型是否存在,缺失时自动拉取。
   - `ensure_responses_supported()`: 检查 Ollama 版本是否支持 Responses API。
   - `supports_responses()`: 版本检查辅助函数,开发版本(0.0.0)始终通过。

2. **client.rs** -- `OllamaClient` 核心客户端:
   - `probe_server()`: 根据是否为 OpenAI 兼容模式,分别探测 `/v1/models` 或 `/api/tags`。
   - `fetch_models()`: 通过 `/api/tags` 获取模型名称列表。
   - `fetch_version()`: 通过 `/api/version` 获取 semver 版本号。
   - `pull_model_stream()`: 通过 `/api/pull` 发起流式模型拉取,返回 `PullEvent` 异步流。
   - `pull_with_reporter()`: 高层拉取辅助函数,驱动进度报告器。

3. **pull.rs** -- 进度报告系统:
   - `PullEvent` 枚举: Status / ChunkProgress / Success / Error。
   - `PullProgressReporter` trait: 事件驱动的进度观察者接口。
   - `CliProgressReporter`: 在 stderr 输出下载进度(含速率计算)。
   - `TuiProgressReporter`: 当前委托给 CLI 报告器。

4. **parser.rs** -- JSON 响应解析,将 Ollama API 响应转换为 `PullEvent`。

5. **url.rs** -- URL 处理工具函数,提取 host root 和判断 OpenAI 兼容性。

## 目录结构

```
ollama/
  Cargo.toml
  src/
    lib.rs       # 入口,ensure_oss_ready() 和版本检查
    client.rs    # OllamaClient,HTTP 通信核心
    pull.rs      # PullEvent, PullProgressReporter 进度系统
    parser.rs    # API 响应 JSON 解析
    url.rs       # URL 处理工具
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-core` | `Config`、`ModelProviderInfo`、`OLLAMA_OSS_PROVIDER_ID` |
| `reqwest` | HTTP 客户端(支持 JSON 和流式响应) |
| `serde_json` | JSON 解析 |
| `semver` | 语义化版本号解析与比较 |
| `bytes` | 流式响应的字节缓冲 |
| `futures` | 异步流抽象 |
| `async-stream` | 异步流生成宏 |
| `tokio` | 异步运行时 |
| `tracing` | 日志记录 |
| `wiremock` | HTTP mock 测试(同时作为普通依赖和测试依赖) |

## 核心接口/API

### 顶层函数

```rust
/// 确保本地 Ollama 推理环境就绪
pub async fn ensure_oss_ready(config: &Config) -> std::io::Result<()>;

/// 确保 Ollama 版本支持 Responses API (>= 0.13.4)
pub async fn ensure_responses_supported(provider: &ModelProviderInfo) -> std::io::Result<()>;
```

### OllamaClient

```rust
pub struct OllamaClient { /* ... */ }

impl OllamaClient {
    pub async fn try_from_oss_provider(config: &Config) -> io::Result<Self>;
    pub async fn fetch_models(&self) -> io::Result<Vec<String>>;
    pub async fn fetch_version(&self) -> io::Result<Option<Version>>;
    pub async fn pull_model_stream(&self, model: &str) -> io::Result<BoxStream<'static, PullEvent>>;
    pub async fn pull_with_reporter(&self, model: &str, reporter: &mut dyn PullProgressReporter) -> io::Result<()>;
}
```

### 进度报告

```rust
pub enum PullEvent {
    Status(String),
    ChunkProgress { digest: String, total: Option<u64>, completed: Option<u64> },
    Success,
    Error(String),
}

pub trait PullProgressReporter {
    fn on_event(&mut self, event: &PullEvent) -> io::Result<()>;
}
```
