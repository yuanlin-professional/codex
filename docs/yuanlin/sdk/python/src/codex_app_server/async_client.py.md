# `async_client.py` -- 异步 JSON-RPC 客户端包装器

## 功能概述

本模块提供了 `AsyncAppServerClient` 类，它是 `AppServerClient`（同步客户端）的异步包装器。通过 `asyncio.to_thread()` 将同步 I/O 操作卸载到线程池执行，同时使用 `asyncio.Lock` 保证对 stdio 传输层的串行访问。所有 RPC 方法均以 `async` 形式对外暴露，与同步客户端保持一一对应的 API 签名。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `AsyncAppServerClient` | 类 | 异步客户端，内部持有同步客户端实例和传输锁 |
| `_call_sync()` | 内部方法 | 在持有传输锁的情况下通过 `asyncio.to_thread` 调用同步方法 |
| `start()` / `close()` | 异步方法 | 启动/关闭底层 app-server 进程 |
| `initialize()` | 异步方法 | 执行 JSON-RPC initialize 握手 |
| `thread_start()` / `thread_resume()` / `thread_list()` 等 | 异步方法 | 线程管理 RPC 方法 |
| `turn_start()` / `turn_interrupt()` / `turn_steer()` | 异步方法 | 轮次管理 RPC 方法 |
| `stream_text()` | 异步生成器 | 流式文本生成，逐块 yield `AgentMessageDeltaNotification` |
| `next_notification()` | 异步方法 | 获取下一个服务端通知 |
| `request_with_retry_on_overload()` | 异步方法 | 带过载重试的泛型 RPC 请求 |

## 依赖关系

- **引用的模块**: `.client`（`AppServerClient`, `AppServerConfig`）, `.generated.v2_all`（各种请求/响应类型）, `.models`
- **被引用方**: `.api`（`AsyncCodex` 使用）

## 实现备注

- 使用单一 `asyncio.Lock`（`_transport_lock`）串行化所有对底层 stdio 传输的访问，因为 stdio 无法安全地从多个线程并发读写
- `stream_text()` 在持有锁的期间通过反复调用 `_next_from_iterator` 逐步消费同步迭代器
- `acquire_turn_consumer` / `release_turn_consumer` 直接委托给同步客户端，不涉及锁（它们仅操作标志位）
