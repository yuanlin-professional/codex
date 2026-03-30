# `function_tool.rs` — 函数调用错误类型定义

该文件定义了 `FunctionCallError` 枚举，包含三种变体：`RespondToModel(String)` 表示需要回传给模型的错误、`MissingLocalShellCallId` 表示 `LocalShellCall` 缺少 `call_id` 或 `id`、`Fatal(String)` 表示致命错误。使用 `thiserror` 派生 `Error` trait，每种变体对应不同的错误显示格式。
