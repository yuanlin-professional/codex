# `model_provider_info.rs` — 模型提供商信息注册与管理

## 功能概述

此文件定义了 Codex 支持的模型提供商（Model Provider）注册表。提供商定义可来自两个位置：编译到二进制文件中的内置默认值，以及用户在 `~/.codex/config.toml` 中 `model_providers` 键下自定义的条目（会在运行时覆盖或扩展默认值）。文件管理了 OpenAI、Ollama、LMStudio 等提供商的基本 URL、认证方式、重试策略和超时配置。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `WireApi` | 枚举 | 提供商使用的通信协议，当前仅支持 `Responses`（`chat` 已移除） |
| `ModelProviderInfo` | 结构体 | 提供商定义的完整序列化表示，包含名称、URL、认证、重试、超时等所有配置 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ModelProviderInfo::to_api_provider` | `(&self, auth_mode: Option<AuthMode>) -> Result<ApiProvider>` | 将提供商信息转换为 API 层使用的 `ApiProvider` 对象 |
| `ModelProviderInfo::api_key` | `(&self) -> Result<Option<String>>` | 从环境变量中获取 API Key，如果 `env_key` 已设置但环境变量缺失则报错 |
| `ModelProviderInfo::create_openai_provider` | `(base_url: Option<String>) -> ModelProviderInfo` | 创建 OpenAI 默认提供商，附带版本和组织头信息 |
| `built_in_model_providers` | `(openai_base_url: Option<String>) -> HashMap<String, ModelProviderInfo>` | 返回内置的默认提供商列表（OpenAI、Ollama、LMStudio） |
| `create_oss_provider` | `(default_provider_port: u16, wire_api: WireApi) -> ModelProviderInfo` | 创建开源提供商实例，支持通过环境变量自定义端口和 URL |
| `build_header_map` | `(&self) -> Result<HeaderMap>` | 构建 HTTP 请求头映射，合并静态头和环境变量头 |

## 依赖关系

- **引用的 crate**: `codex_api`（`Provider`、`RetryConfig`）、`http`（`HeaderMap`）、`schemars`、`serde`、`crate::auth::AuthMode`、`crate::error`
- **被引用方**: `lib.rs` 公开导出多个常量和类型、`api_bridge.rs`、`auth_env_telemetry.rs`、`thread_manager` 等

## 实现备注

- `WireApi` 的反序列化包含对已移除 `chat` 协议的显式错误提示，引导用户迁移到 `responses`
- `ollama-chat` 提供商 ID 同样已废弃，提供了迁移错误信息
- 重试次数有硬上限（`MAX_STREAM_MAX_RETRIES = 100`、`MAX_REQUEST_MAX_RETRIES = 100`）
- 默认流空闲超时为 300 秒，WebSocket 连接超时为 15 秒
- OpenAI 提供商支持 `requires_openai_auth` 和 `supports_websockets`，而开源提供商默认不启用
