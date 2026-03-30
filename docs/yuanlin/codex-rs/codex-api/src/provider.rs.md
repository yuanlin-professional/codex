# `provider.rs` — API 提供者配置

## 功能概述

定义 `Provider` 结构体和 `RetryConfig`，封装 API 端点的 base URL、默认头、查询参数、重试策略和流式 idle timeout。提供 URL 拼接（`url_for_path`）、请求构建（`build_request`）、WebSocket URL 转换和 Azure 端点检测等辅助方法。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Provider` | struct | API 端点配置（name/base_url/headers/query_params/retry/timeout） |
| `RetryConfig` | struct | 重试配置（max_attempts/base_delay/retry_429/retry_5xx/retry_transport） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `url_for_path` | `fn(&self, path) -> String` | 拼接完整 URL（含查询参数） |
| `build_request` | `fn(&self, method, path) -> Request` | 构建带默认头的请求 |
| `websocket_url_for_path` | `fn(&self, path) -> Result<Url>` | HTTP URL 转为 WebSocket URL |
| `is_azure_responses_wire_base_url` | `fn(name, base_url) -> bool` | 检测 Azure 端点 |

## 依赖关系

- **引用的 crate**: `codex_client`, `url`
- **被引用方**: 所有 endpoint 客户端

## 实现备注

- Azure 检测通过名称和 URL 中的多个标记（openai.azure、cognitiveservices.azure 等）。
