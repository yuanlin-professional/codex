# `responsesProxy.ts` -- 测试用 Responses API 代理服务器

## 功能概述

`responsesProxy.ts` 提供了一个轻量级的 HTTP 测试代理服务器，模拟 OpenAI Responses API 的 SSE（Server-Sent Events）流式响应。它在本地随机端口上监听，记录所有收到的请求，并根据预配置的响应体序列返回 SSE 事件流。此外还提供了一组工厂函数用于构造常见的 SSE 事件。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| `SseEvent` | type | SSE 事件对象，包含 `type` 字段和任意附加数据 |
| `SseResponseBody` | type | SSE 响应体，包含事件数组 |
| `ResponsesProxyOptions` | type | 代理配置，包含响应体序列和可选状态码 |
| `ResponsesProxy` | type | 代理实例，包含 URL、关闭函数和已记录的请求 |
| `RecordedRequest` | type | 已记录的请求，包含原始 body、解析后的 JSON 和 headers |
| `ResponsesApiRequest` | type | Responses API 请求结构 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `startResponsesTestProxy` | `(options): Promise<ResponsesProxy>` | 启动测试代理服务器 |
| `sse` | `(...events): SseResponseBody` | 将多个事件组合为一个 SSE 响应体 |
| `responseStarted` | `(responseId?): SseEvent` | 构造 `response.created` 事件 |
| `assistantMessage` | `(text, itemId?): SseEvent` | 构造助手消息的 `response.output_item.done` 事件 |
| `shell_call` | `(): SseEvent` | 构造 shell 函数调用事件 |
| `responseFailed` | `(errorMessage): SseEvent` | 构造错误事件 |
| `responseCompleted` | `(responseId?, usage?): SseEvent` | 构造 `response.completed` 事件，含 token 用量 |

## 依赖关系

- **引用的模块**: `node:http`
- **被引用方**: `tests/abort.test.ts`、`tests/run.test.ts`、`tests/runStreamed.test.ts`

## 实现备注

- 响应体可以是数组或生成器，支持有限响应和无限响应（如用于取消测试的无限 shell 调用）。
- 代理仅处理 `POST /responses` 请求，其他请求返回 404。
- 服务器绑定到 `127.0.0.1:0`（随机端口），避免端口冲突。
- 默认 token 用量为 42 输入/12 缓存/5 输出 token。
