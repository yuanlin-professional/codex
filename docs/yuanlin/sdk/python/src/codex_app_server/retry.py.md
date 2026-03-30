# `retry.py` -- 过载重试辅助函数

`retry_on_overload()` 函数实现了带指数退避和抖动的重试逻辑，专门用于处理服务器过载类型的瞬态错误。它接受一个无参可调用对象 `op`，在遇到 `is_retryable_error()` 判定为可重试的异常时自动重试，支持配置最大尝试次数（`max_attempts`）、初始延迟（`initial_delay_s`）、最大延迟（`max_delay_s`）和抖动比例（`jitter_ratio`）。每次重试后延迟翻倍，但不超过最大延迟上限。该函数被 `AppServerClient.request_with_retry_on_overload()` 和示例代码直接使用。
