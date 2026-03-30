# `chatgpt_client.rs` — ChatGPT 后端 API HTTP 客户端

## 功能概述
封装向 ChatGPT 后端 API 发送 GET 请求的通用逻辑。负责初始化认证令牌、构建带有 Bearer 认证和 account-id 头的请求，并反序列化 JSON 响应。支持可选超时配置。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `chatgpt_get_request` | `async fn<T>(config, path) -> Result<T>` | 不带超时的 GET 请求 |
| `chatgpt_get_request_with_timeout` | `async fn<T>(config, path, timeout) -> Result<T>` | 带可选超时的 GET 请求 |

## 依赖关系
- **引用的 crate**: `codex_core`, `anyhow`, `serde`, `reqwest`
- **被引用方**: `connectors.rs`, `get_task.rs`
