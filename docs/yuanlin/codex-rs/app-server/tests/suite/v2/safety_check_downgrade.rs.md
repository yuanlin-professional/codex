# `safety_check_downgrade.rs` — 测试模块

## 测试覆盖范围
测试安全检查降级（safety check downgrade）通知，验证当 OpenAI 响应头或响应体中的模型字段
与请求模型不匹配时，发送模型重路由（model_rerouted）通知。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `openai_model_header_mismatch_emits_model_rerouted_notification_v2` | 响应头中的模型不匹配时发送重路由通知 |
| `response_model_field_mismatch_emits_model_rerouted_notification_v2_when_header_matches_requested` | 响应体模型字段不匹配时发送重路由通知 |
