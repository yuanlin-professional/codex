# `pending_input.rs` — 测试模块

## 测试覆盖范围
待处理输入（Pending Input）的排队与处理，验证在流式响应进行中注入的用户输入能正确触发后续请求。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `injected_user_input_triggers_follow_up_request_with_deltas` | 在流式响应期间注入的用户输入触发包含增量的后续请求（标记为 flaky，暂时忽略） |
