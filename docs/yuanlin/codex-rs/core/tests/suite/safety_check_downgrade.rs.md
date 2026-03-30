# `safety_check_downgrade.rs` -- 测试模块

## 测试覆盖范围
测试当 OpenAI 服务器返回的模型与请求模型不匹配时的安全检查降级行为，包括模型重路由警告事件的发送、每轮仅发送一次警告，以及仅大小写差异时不触发警告。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `openai_model_header_mismatch_emits_warning_event_and_warning_item` | HTTP 响应头 OpenAI-Model 与请求模型不一致时，发出 ModelReroute 事件和 Warning 事件，并在历史中记录警告消息 |
| `response_model_field_mismatch_emits_warning_when_header_matches_requested` | 响应体内 response.created headers 中的模型与请求不一致（但 HTTP header 匹配请求），仍触发 ModelReroute 和高风险网络活动警告 |
| `openai_model_header_mismatch_only_emits_one_warning_per_turn` | 同一 turn 中多次请求（含工具调用后续请求）模型不匹配时，只发出一次警告事件 |
| `openai_model_header_casing_only_mismatch_does_not_warn` | 当模型名称仅大小写不同时，不触发任何 ModelReroute 或 Warning 事件 |
