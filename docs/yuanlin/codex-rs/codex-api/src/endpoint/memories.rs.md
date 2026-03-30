# `memories.rs` — 记忆摘要客户端

## 功能概述

`MemoriesClient` 提供对 `memories/trace_summarize` API 端点的调用，用于将原始 trace 数据通过模型摘要为结构化记忆。支持直接 JSON body 和结构化 `MemorySummarizeInput` 两种输入方式。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `MemoriesClient<T, A>` | struct | 记忆摘要端点客户端 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `summarize` | `async fn(&self, body, headers) -> Result<Vec<MemorySummarizeOutput>>` | POST 请求并解析摘要输出 |
| `summarize_input` | `async fn(&self, input, headers) -> Result<Vec<MemorySummarizeOutput>>` | 结构化输入版 |

## 依赖关系

- **被引用方**: `lib.rs`

## 实现备注

- 包含完整的单元测试验证请求 URL、HTTP 方法和 body 结构。
