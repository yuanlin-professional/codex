# `tests.rs` — 测试模块

## 测试覆盖范围

覆盖 Guardian 审查系统的完整功能，包括：转录提取与过滤、文本截断、JSON 序列化与反序列化、审批请求格式化、Guardian session 配置构建、审查请求布局快照验证、prompt-cache key 复用、并行审查分叉、错误处理与事件流等端到端行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `build_guardian_transcript_keeps_original_numbering` | 转录渲染应保持原始编号 |
| `collect_guardian_transcript_entries_skips_contextual_user_messages` | 应跳过合成的环境上下文用户消息 |
| `collect_guardian_transcript_entries_includes_recent_tool_calls_and_output` | 应包含近期工具调用和结果 |
| `guardian_truncate_text_keeps_prefix_suffix_and_xml_marker` | 文本截断应保留前后部分并插入 XML 标记 |
| `format_guardian_action_pretty_truncates_large_string_fields` | 美化格式化应截断大字符串字段 |
| `guardian_approval_request_to_json_renders_mcp_tool_call_shape` | MCP 工具调用的 JSON 序列化格式验证 |
| `guardian_assessment_action_value_redacts_apply_patch_patch_text` | ApplyPatch 的评估摘要应省略 patch 文本 |
| `guardian_request_turn_id_prefers_network_access_owner_turn` | NetworkAccess 应使用自身 turn_id 而非默认值 |
| `cancelled_guardian_review_emits_terminal_abort_without_warning` | 取消审查应发送 Abort 事件但不发 Warning |
| `routes_approval_to_guardian_requires_auto_only_review_policy` | Guardian 路由需要 GuardianSubagent 审查策略 |
| `build_guardian_transcript_reserves_separate_budget_for_tool_evidence` | 工具证据应有独立的 token 预算 |
| `parse_guardian_assessment_extracts_embedded_json` | 应能从包裹文本中提取嵌入的评估 JSON |
| `guardian_review_request_layout_matches_model_visible_request_snapshot` | 审查请求布局快照验证（端到端） |
| `guardian_reuses_prompt_cache_key_and_appends_prior_reviews` | 后续审查应复用 prompt-cache key 并附加先前审查结果 |
| `guardian_review_surfaces_responses_api_errors_in_rejection_reason` | API 错误应在拒绝原因中体现 |
| `guardian_parallel_reviews_fork_from_last_committed_trunk_history` | 并行审查应从最后提交的主干历史分叉，不包含进行中的审查 |
| `guardian_review_session_config_preserves_parent_network_proxy` | Guardian 配置应保留父级网络代理设置 |
| `guardian_review_session_config_overrides_parent_developer_instructions` | Guardian 应覆盖父级开发者指令为策略提示词 |
| `guardian_review_session_config_uses_live_network_proxy_state` | Guardian 应使用实时网络代理状态而非静态配置 |
| `guardian_review_session_config_rejects_pinned_collab_feature` | 当 collab 功能被要求层强制开启时应报错 |
| `guardian_review_session_config_uses_parent_active_model_instead_of_hardcoded_slug` | Guardian 应使用运行时活跃模型而非硬编码名称 |
| `guardian_review_session_config_uses_requirements_guardian_override` | 应使用 requirements 中的 guardian 策略覆盖 |
| `guardian_review_session_config_uses_default_guardian_policy_without_requirements_override` | 无 requirements 覆盖时应使用默认 guardian 策略 |
