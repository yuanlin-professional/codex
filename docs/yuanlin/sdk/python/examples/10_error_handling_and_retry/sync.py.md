# `sync.py` -- 错误处理与重试示例（同步版）

演示同步场景下的错误处理和重试策略。使用 SDK 内置的 `retry_on_overload()` 函数包装 turn 执行，支持配置最大尝试次数和退避参数。捕获 `ServerBusyError`（过载）和 `JsonRpcError`（通用 RPC 错误），并通过 `TurnStatus.failed` 枚举检查 turn 是否失败。
