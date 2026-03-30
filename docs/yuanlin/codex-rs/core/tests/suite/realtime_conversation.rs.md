# `realtime_conversation.rs` -- 测试模块

## 测试覆盖范围
大型测试文件，覆盖实时对话（realtime conversation）功能的完整生命周期，包括会话开始/关闭、音频/文本消息的发送与接收、WebSocket 握手验证、认证方式（API key vs ChatGPT auth）、会话替换、传输关闭事件、错误处理（启动前发消息、预检失败、连接失败）、启动上下文注入（线程历史/工作区地图/截断/自定义覆盖/空覆盖禁用），以及 handoff 机制（出站 handoff 镜像、入站 handoff 启动 turn、活跃转录管理、跨 item_done 持久化、非阻塞音频转发、活跃 turn 引导）。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `conversation_start_audio_text_close_round_trip` | 完整的开始-音频-文本-关闭往返，验证事件和 WebSocket 请求 |
| `conversation_start_uses_openai_env_key_fallback_with_chatgpt_auth` | ChatGPT auth 下使用 OPENAI_API_KEY 环境变量作为 realtime 认证 |
| `conversation_transport_close_emits_closed_event` | 传输关闭后发出 RealtimeConversationClosed 事件 |
| `conversation_audio_before_start_emits_error` | 未启动会话时发送音频触发 BadRequest 错误 |
| `conversation_start_preflight_failure_emits_realtime_error_only` | 预检失败（无 API key）发出 realtime Error 事件但不发 closed |
| `conversation_start_connect_failure_emits_realtime_error_only` | 连接失败发出 realtime Error 事件但不发 closed |
| `conversation_text_before_start_emits_error` | 未启动会话时发送文本触发 BadRequest 错误 |
| `conversation_second_start_replaces_runtime` | 第二次 start 替换旧运行时，新音频路由到新会话 |
| `conversation_uses_experimental_realtime_ws_base_url_override` | 使用配置覆盖的 realtime WebSocket 基础 URL |
| `conversation_uses_experimental_realtime_ws_backend_prompt_override` | 使用配置覆盖的后端 prompt |
| `conversation_uses_experimental_realtime_ws_startup_context_override` | 自定义 startup context 替换默认启动上下文 |
| `conversation_disables_realtime_startup_context_with_empty_override` | 空字符串 startup context 禁用启动上下文注入 |
| `conversation_start_injects_startup_context_from_thread_history` | 从线程历史和工作区地图生成启动上下文 |
| `conversation_startup_context_falls_back_to_workspace_map` | 无线程历史时回退到工作区地图 |
| `conversation_startup_context_is_truncated_and_sent_once_per_start` | 启动上下文超长时被截断到 20.5KB 内，仅在 start 时发送一次 |
| `conversation_mirrors_assistant_message_text_to_realtime_handoff` | 助手消息通过 handoff.append 镜像到 realtime |
| `conversation_handoff_persists_across_item_done_until_turn_complete` | handoff 跨 item_done 持久化直到 turn 完成 |
| `inbound_handoff_request_starts_turn` | 入站 handoff 请求启动新的 agent turn |
| `inbound_handoff_request_uses_active_transcript` | 入站 handoff 使用活跃转录（含 assistant/user 角色前缀） |
| `inbound_handoff_request_clears_active_transcript_after_each_handoff` | 每次 handoff 后清除活跃转录 |
| `inbound_conversation_item_does_not_start_turn_and_still_forwards_audio` | conversation.item.added 不启动 turn 但仍转发音频 |
| `delegated_turn_user_role_echo_does_not_redelegate_and_still_forwards_audio` | 委托 turn 的 user 角色回声不触发重新委托 |
| `inbound_handoff_request_does_not_block_realtime_event_forwarding` | 入站 handoff 不阻塞 realtime 音频事件转发 |
| `inbound_handoff_request_steers_active_turn` | 活跃 turn 期间的入站 handoff 引导当前 turn |
| `inbound_handoff_request_starts_turn_and_does_not_block_realtime_audio` | 入站 handoff 启动 turn 且不阻塞 realtime 音频 |
