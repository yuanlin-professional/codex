# `compact.rs` — 历史压缩客户端

## 功能概述

`CompactClient` 提供对 `responses/compact` API 端点的调用，支持直接传入 JSON body 或结构化的 `CompactionInput`。将响应解析为 `Vec<ResponseItem>` 返回，用于对话历史的压缩。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CompactClient<T, A>` | struct | 压缩端点客户端，泛型参数为 transport 和 auth |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `compact` | `async fn(&self, body, headers) -> Result<Vec<ResponseItem>>` | 发送 POST 请求并解析输出 |
| `compact_input` | `async fn(&self, input, headers) -> Result<Vec<ResponseItem>>` | 从结构化输入构造请求 |

## 依赖关系

- **引用的 crate**: `codex_client`, `codex_protocol`
- **被引用方**: `lib.rs`（公开导出）
