# `error_code.rs` — JSON-RPC 错误码常量定义

本模块定义了 app-server 使用的 JSON-RPC 标准及自定义错误码常量：`INVALID_REQUEST_ERROR_CODE`（-32600）、`INVALID_PARAMS_ERROR_CODE`（-32602）、`INTERNAL_ERROR_CODE`（-32603）、`OVERLOADED_ERROR_CODE`（-32001，自定义服务过载错误）以及 `INPUT_TOO_LARGE_ERROR_CODE`（字符串 `"input_too_large"`）。这些常量在整个 app-server 各模块中被广泛引用。
