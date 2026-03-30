# `models.rs` — 模型列表客户端

## 功能概述

`ModelsClient` 提供对 `models` API 端点的调用，获取可用模型列表。自动附加 `client_version` 查询参数，并解析响应中的 ETag 头，用于客户端缓存。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ModelsClient<T, A>` | struct | 模型列表端点客户端 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `list_models` | `async fn(&self, client_version, headers) -> Result<(Vec<ModelInfo>, Option<String>)>` | 获取模型列表和 ETag |

## 依赖关系

- **引用的 crate**: `codex_protocol::openai_models`
- **被引用方**: `lib.rs`

## 实现备注

- 使用 `execute_with` 回调在请求上追加查询参数。
- 包含详细单元测试验证 URL 拼接、模型解析和 ETag 提取。
