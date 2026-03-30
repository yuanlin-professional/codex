# `client.rs` — 测试模块

## 测试覆盖范围

测试模型客户端(ModelClient)的请求构建与响应处理功能，包括会话恢复时的消息重放、请求头(conversation_id/model)、基础指令覆盖、ChatGPT 认证、API Key 认证、用户指令消息、开发者指令消息、推理参数(effort/summary/verbosity)、协作模式(collaboration mode)覆盖、Azure 适配、速率限制、上下文窗口错误、内容过滤、环境变量覆盖认证，以及跨轮次的历史去重。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `resume_includes_initial_messages_and_sends_prior_items` | 恢复会话时包含初始消息并发送先前的对话项 |
| `resume_replays_legacy_js_repl_image_rollout_shapes` | 恢复会话时正确重放旧版 JS REPL 图片 rollout 格式 |
| `resume_replays_image_tool_outputs_with_detail` | 恢复会话时正确重放带 detail 属性的图片工具输出 |
| `includes_conversation_id_and_model_headers_in_request` | 请求中包含 conversation_id 和 model 头信息 |
| `includes_base_instructions_override_in_request` | 请求中包含基础指令覆盖 |
| `chatgpt_auth_sends_correct_request` | ChatGPT 认证模式下发送正确的请求格式 |
| `prefers_apikey_when_config_prefers_apikey_even_with_chatgpt_tokens` | 配置优先 API Key 时即使存在 ChatGPT Token 也使用 API Key |
| `includes_user_instructions_message_in_request` | 请求中包含用户指令消息 |
| `includes_apps_guidance_as_developer_message_for_chatgpt_auth` | ChatGPT 认证时包含应用引导作为开发者消息 |
| `omits_apps_guidance_for_api_key_auth_even_when_feature_enabled` | API Key 认证时即使功能启用也省略应用引导 |
| `skills_append_to_developer_message` | 技能(skills)追加到开发者消息中 |
| `includes_configured_effort_in_request` | 请求中包含配置的推理努力度(effort) |
| `includes_no_effort_in_request` | 未配置 effort 时请求中不包含该字段 |
| `includes_default_reasoning_effort_in_request_when_defined_by_model_info` | 模型信息定义默认 effort 时请求中包含该值 |
| `user_turn_collaboration_mode_overrides_model_and_effort` | 协作模式覆盖模型和 effort 参数 |
| `configured_reasoning_summary_is_sent` | 请求中发送配置的推理摘要(reasoning summary) |
| `user_turn_explicit_reasoning_summary_overrides_model_catalog_default` | 显式指定的推理摘要覆盖模型目录默认值 |
| `reasoning_summary_is_omitted_when_disabled` | 禁用时省略推理摘要 |
| `reasoning_summary_none_overrides_model_catalog_default` | None 值覆盖模型目录的推理摘要默认值 |
| `includes_default_verbosity_in_request` | 请求中包含默认详细度(verbosity) |
| `configured_verbosity_not_sent_for_models_without_support` | 不支持详细度的模型不发送该字段 |
| `configured_verbosity_is_sent` | 请求中发送配置的详细度 |
| `includes_developer_instructions_message_in_request` | 请求中包含开发者指令消息 |
| `azure_responses_request_includes_store_and_reasoning_ids` | Azure 请求中包含 store 和 reasoning ID 字段 |
| `token_count_includes_rate_limits_snapshot` | Token 计数事件中包含速率限制快照 |
| `usage_limit_error_emits_rate_limit_event` | 使用量限制错误触发速率限制事件 |
| `context_window_error_sets_total_tokens_to_model_window` | 上下文窗口错误时将总 token 数设为模型窗口大小 |
| `incomplete_response_emits_content_filter_error_message` | 不完整响应(内容过滤)时发出对应错误消息 |
| `azure_overrides_assign_properties_used_for_responses_url` | Azure 覆盖正确分配用于 responses URL 的属性 |
| `env_var_overrides_loaded_auth` | 环境变量覆盖已加载的认证信息 |
| `history_dedupes_streamed_and_final_messages_across_turns` | 跨轮次去重流式和最终消息的历史记录 |
