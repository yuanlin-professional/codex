# `mock_model_server.rs` — 模拟模型 API 服务器

## 功能概述
提供创建模拟 OpenAI Responses API 服务器的函数。支持按序列返回预定义的 SSE 响应
（`create_mock_responses_server_sequence`，带调用次数断言）、不限次数的序列响应
（`create_mock_responses_server_sequence_unchecked`）以及重复返回相同助手消息的简单模式
（`create_mock_responses_server_repeating_assistant`）。底层使用 `wiremock` 和自定义 `SeqResponder`。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_mock_responses_server_sequence` | `pub async fn ...(responses: Vec<String>) -> MockServer` | 创建按顺序返回响应的模拟服务器，验证调用次数 |
| `create_mock_responses_server_sequence_unchecked` | `pub async fn ...(responses: Vec<String>) -> MockServer` | 同上但不验证调用次数 |
| `create_mock_responses_server_repeating_assistant` | `pub async fn ...(message: &str) -> MockServer` | 创建对每个请求返回相同助手消息的模拟服务器 |

## 依赖关系
- **引用的 crate**: `wiremock`, `core_test_support::responses`
- **被引用方**: `common/lib.rs` 导出，供 turn_start、command_exec 等需要模型响应的测试使用
