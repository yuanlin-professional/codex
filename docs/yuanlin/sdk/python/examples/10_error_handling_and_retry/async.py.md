# `async.py` -- 错误处理与重试示例（异步版）

演示异步场景下的错误处理和重试策略。实现了自定义的 `retry_on_overload_async()` 异步重试函数，使用 `is_retryable_error()` 判断异常是否可重试，支持指数退避和抖动。捕获 `ServerBusyError` 和 `JsonRpcError` 异常，并通过 `TurnStatus.failed` 枚举检查 turn 是否失败。
