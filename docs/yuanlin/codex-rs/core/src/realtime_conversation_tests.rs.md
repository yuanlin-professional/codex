# `realtime_conversation_tests.rs` — 测试模块

## 测试覆盖范围

测试实时对话（realtime conversation）的 handoff 请求文本提取和 handoff 状态管理逻辑，验证 `realtime_text_from_handoff_request` 在不同输入下的文本提取行为，以及 `RealtimeHandoffState` 的主动清除。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `extracts_text_from_handoff_request_active_transcript` | 当 handoff 请求包含 `active_transcript` 时，从中提取对话文本（格式为 `role: text`），忽略 `input_transcript` |
| `extracts_text_from_handoff_request_input_transcript_if_messages_missing` | 当 `active_transcript` 为空时，回退使用 `input_transcript` 的文本 |
| `ignores_empty_handoff_request_input_transcript` | 当 `active_transcript` 为空且 `input_transcript` 也为空字符串时，返回 `None` |
| `clears_active_handoff_explicitly` | `RealtimeHandoffState` 的 `active_handoff` 可以被显式设置和清除 |
