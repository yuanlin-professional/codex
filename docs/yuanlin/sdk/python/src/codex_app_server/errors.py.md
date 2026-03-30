# `errors.py` -- SDK 错误层次结构与 JSON-RPC 错误映射

## 功能概述

本模块定义了 SDK 的完整异常类层次结构，并提供了将原始 JSON-RPC 错误码映射到具体异常类的 `map_jsonrpc_error()` 函数。异常层次从通用的 `AppServerError` 基类开始，经过 `JsonRpcError`、`AppServerRpcError` 逐步细化到具体的错误类型如 `ParseError`、`ServerBusyError` 等。模块还包含服务器过载检测逻辑和可重试错误判断函数。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppServerError` | 异常类 | SDK 所有异常的基类 |
| `JsonRpcError` | 异常类 | 原始 JSON-RPC 错误，携带 `code`、`message`、`data` |
| `TransportClosedError` | 异常类 | 传输层意外关闭错误 |
| `AppServerRpcError` | 异常类 | 已分类的 JSON-RPC 错误基类 |
| `ParseError` | 异常类 | JSON-RPC -32700 解析错误 |
| `InvalidRequestError` | 异常类 | JSON-RPC -32600 无效请求 |
| `MethodNotFoundError` | 异常类 | JSON-RPC -32601 方法不存在 |
| `InvalidParamsError` | 异常类 | JSON-RPC -32602 参数无效 |
| `InternalRpcError` | 异常类 | JSON-RPC -32603 内部错误 |
| `ServerBusyError` | 异常类 | 服务器过载/不可用，调用者应重试 |
| `RetryLimitExceededError` | 异常类 | 服务器内部重试预算已耗尽 |
| `map_jsonrpc_error()` | 函数 | 将 JSON-RPC 错误码映射为具体的 SDK 异常实例 |
| `is_retryable_error()` | 函数 | 判断异常是否为可重试的瞬态过载错误 |

## 依赖关系

- **引用的模块**: 无外部依赖（仅 `typing`）
- **被引用方**: `.client`（错误映射）, `.retry`（可重试判断）, `.__init__`（重导出）

## 实现备注

- 服务器过载检测 `_is_server_overloaded()` 采用递归搜索策略，支持在 `data` 字段的多种嵌套结构中查找 `server_overloaded` 标记
- JSON-RPC 错误码 -32000 到 -32099 范围为服务器自定义错误，在此范围内根据 data 内容进一步细分
- `RetryLimitExceededError` 继承自 `ServerBusyError`，通过消息文本中的 "retry limit" 或 "too many failed attempts" 关键词识别
