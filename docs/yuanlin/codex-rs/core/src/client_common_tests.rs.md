# `client_common_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖 Responses API 请求序列化的通用逻辑，包括 text 控制参数（verbosity、JSON schema 格式）的序列化与省略、service_tier 字段的序列化，以及 shell 输出的重新序列化（reserialize_shell_outputs）将原始 JSON 元数据转换为人类可读格式。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `serializes_text_verbosity_when_set` | 验证设置 text verbosity 为 "low" 时正确序列化到 JSON |
| `serializes_text_schema_with_strict_format` | 验证设置 JSON schema 格式时序列化包含 name、type、strict 和 schema 字段 |
| `omits_text_when_not_set` | 验证 text 为 None 时 JSON 中不包含 text 字段 |
| `serializes_flex_service_tier_when_set` | 验证 service_tier 设置为 "flex" 时正确序列化 |
| `reserializes_shell_outputs_for_function_and_custom_tool_calls` | 验证 FunctionCallOutput 和 CustomToolCallOutput 中的 shell JSON 输出被转换为人类可读的文本格式 |
