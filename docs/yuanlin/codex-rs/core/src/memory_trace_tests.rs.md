# `memory_trace_tests.rs` — 测试模块

## 测试覆盖范围

测试 memory trace 文件的加载与规范化逻辑，包括 `normalize_trace_items` 对 `response_item` 载荷解包、角色过滤的处理，`load_trace_items` 对 JSONL 中数组与对象混合格式的支持，以及 `load_trace_text` 对 UTF-8 BOM 编码文件的正确解码。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `normalize_trace_items_handles_payload_wrapper_and_message_role_filtering` | 正确解包 `response_item` 类型的载荷（单对象和数组），过滤掉 `tool` 角色的消息，保留 `assistant`、`user`、`developer` 角色，非 `response_item` 类型直接保留 |
| `load_trace_items_supports_jsonl_arrays_and_objects` | JSONL 文件中的单行对象和数组均能正确加载，`tool` 角色消息被过滤 |
| `load_trace_text_decodes_utf8_sig` | 包含 UTF-8 BOM（0xEF 0xBB 0xBF）的 trace 文件能正确解码，去除 BOM 前缀 |
