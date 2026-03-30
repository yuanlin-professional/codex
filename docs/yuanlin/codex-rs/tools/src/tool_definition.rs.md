# `tool_definition.rs` -- 工具定义数据结构

工具元数据和 schema 的核心结构 `ToolDefinition`，包含名称、描述、input_schema（`JsonSchema`）、可选的 output_schema（`serde_json::Value`）和 defer_loading 标志。提供 `renamed` 方法修改工具名称和 `into_deferred` 方法将工具转为延迟加载模式（清除 output_schema 并设置 defer_loading）。
