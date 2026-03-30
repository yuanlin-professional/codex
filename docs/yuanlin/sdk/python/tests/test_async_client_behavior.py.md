# `test_async_client_behavior.py` -- 测试模块

## 测试覆盖范围

验证 `AsyncAppServerClient` 的并发行为特性，确保底层 stdio 传输的串行化访问和流式文本生成的正确性。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_async_client_serializes_transport_calls` | 验证并发的异步调用被传输锁串行化（最大活跃数为 1） |
| `test_async_stream_text_is_incremental_and_blocks_parallel_calls` | 验证流式文本生成逐块产出且在流式期间阻塞其他调用 |
