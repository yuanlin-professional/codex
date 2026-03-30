# `compact_tests.rs` — 测试模块

## 测试覆盖范围

对话历史压缩（compaction）功能，包括内容项文本提取、用户消息收集、token 限制的压缩历史构建、初始上下文注入、开发者消息替换、会话前缀过滤、模型切换消息重注入、压缩项（Compaction）处理。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `content_items_to_text_joins_non_empty_segments` | 内容项文本拼接非空片段 |
| `content_items_to_text_ignores_image_only_content` | 内容项文本忽略纯图片内容 |
| `collect_user_messages_extracts_user_text_only` | 收集用户消息仅提取用户文本 |
| `collect_user_messages_filters_session_prefix_entries` | 收集用户消息时过滤会话前缀条目 |
| `build_token_limited_compacted_history_truncates_overlong_user_messages` | token 限制的压缩历史截断过长用户消息 |
| `build_token_limited_compacted_history_appends_summary_message` | 压缩历史追加摘要消息 |
| `process_compacted_history_replaces_developer_messages` | 处理压缩历史时替换开发者消息 |
| `process_compacted_history_reinjects_full_initial_context` | 处理压缩历史时重注入完整初始上下文 |
| `process_compacted_history_drops_non_user_content_messages` | 处理压缩历史时丢弃非用户内容消息 |
| `process_compacted_history_inserts_context_before_last_real_user_message_only` | 仅在最后一个真实用户消息前插入上下文 |
| `process_compacted_history_reinjects_model_switch_message` | 处理压缩历史时重注入模型切换消息 |
| `insert_initial_context_before_last_real_user_or_summary_keeps_summary_last` | 在最后真实用户/摘要消息前插入上下文保持摘要在末尾 |
| `insert_initial_context_before_last_real_user_or_summary_keeps_compaction_last` | 压缩项（Compaction）保持在末尾 |
