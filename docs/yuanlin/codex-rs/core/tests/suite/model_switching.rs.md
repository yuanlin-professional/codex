# `model_switching.rs` — 测试模块

## 测试覆盖范围
模型切换功能，包括模型变更时的指令注入、service_tier 切换、图像模态兼容性处理、图片生成历史回放、线程回滚以及上下文窗口更新等。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `model_change_appends_model_instructions_developer_message` | 模型变更后在 developer 消息中追加 model_switch 指令 |
| `model_and_personality_change_only_appends_model_instructions` | 同时变更模型和个性时只追加模型切换指令，不追加个性更新 |
| `service_tier_change_is_applied_on_next_http_turn` | service_tier 变更在下一次 HTTP 请求中正确应用 |
| `flex_service_tier_is_applied_to_http_turn` | flex service_tier 正确应用到 HTTP 请求 |
| `model_change_from_image_to_text_strips_prior_image_content` | 从图像模型切换到纯文本模型时剥离历史中的图像内容 |
| `generated_image_is_replayed_for_image_capable_models` | 图像支持模型中生成的图像在后续请求中正确回放 |
| `model_change_from_generated_image_to_text_preserves_prior_generated_image_call` | 切换到纯文本模型时保留生成图像调用但清空图像数据 |
| `thread_rollback_after_generated_image_drops_entire_image_turn_history` | 线程回滚后正确移除包含生成图像的整个轮次历史 |
| `model_switch_to_smaller_model_updates_token_context_window` | 切换到更小上下文窗口的模型时正确更新 token 上下文窗口 |
