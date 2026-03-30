# `codex_tests.rs` — 测试模块

## 测试覆盖范围

该文件（约 5286 行）是 `codex.rs` 的核心测试模块，覆盖了 Session 创建、Turn 生命周期、对话历史管理、rollout 持久化与恢复、上下文注入与 diff、MCP 工具筛选、审批流程、steer 输入、实时对话集成、Guardian 子代理关闭等关键路径。还包含辅助函数 `make_session_and_context`、`make_session_and_context_with_rx`、`make_session_and_context_with_dynamic_tools_and_rx` 用于构建测试用的 Session 和 TurnContext。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `regular_turn_emits_turn_started_without_waiting_for_startup_prewarm` | 普通 Turn 不等待 startup prewarm 即发出 TurnStarted 事件 |
| `interrupting_regular_turn_waiting_on_startup_prewarm_emits_turn_aborted` | 中断等待 startup prewarm 的 Turn 发出 TurnAborted 事件 |
| `assistant_message_stream_parsers_can_be_seeded_from_output_item_added_text` | 流式解析器可从 output_item_added 的初始文本中播种并正确剥离 citation |
| `assistant_message_stream_parsers_seed_buffered_prefix_stays_out_of_finish_tail` | 播种时的缓冲前缀不会出现在 finish 尾部 |
| `assistant_message_stream_parsers_seed_plan_parser_across_added_and_delta_boundaries` | Plan 解析器跨 added/delta 边界正确分段 |
| `validated_network_policy_amendment_host_allows_normalized_match` | 网络策略修正的 host 归一化匹配 |
| `validated_network_policy_amendment_host_rejects_mismatch` | 网络策略修正的 host 不匹配时拒绝 |
| `start_managed_network_proxy_applies_execpolicy_network_rules` | 托管网络代理应用 execpolicy 网络规则 |
| `start_managed_network_proxy_ignores_invalid_execpolicy_network_rules` | 托管网络代理忽略无效的 execpolicy 网络规则 |
| `get_base_instructions_no_user_content` | 获取基础指令（无用户内容）的正确性 |
| `reload_user_config_layer_updates_effective_apps_config` | 重新加载用户配置层后更新 apps 配置 |
| `filter_connectors_for_input_skips_duplicate_slug_mentions` | 筛选连接器时跳过重复 slug 引用 |
| `filter_connectors_for_input_skips_when_skill_name_conflicts` | 筛选连接器时跳过与技能名冲突的引用 |
| `filter_connectors_for_input_skips_disabled_connectors` | 筛选连接器时跳过已禁用连接器 |
| `filter_connectors_for_input_skips_plugin_mentions` | 筛选连接器时跳过 plugin:// 引用 |
| `collect_explicit_app_ids_from_skill_items_*` | 从技能项中收集显式 app ID 的多种场景 |
| `non_app_mcp_tools_remain_visible_without_search_selection` | 非 App MCP 工具在无搜索选择时保持可见 |
| `search_tool_selection_keeps_codex_apps_tools_without_mentions` | 搜索工具选择保留无引用的 codex_apps 工具 |
| `apps_mentions_add_codex_apps_tools_to_search_selected_set` | App 引用将 codex_apps 工具加入搜索选择集 |
| `reconstruct_history_matches_live_compactions` | 从 rollout 重建历史与实时压缩结果一致 |
| `reconstruct_history_uses_replacement_history_verbatim` | 重建历史使用 replacement_history 原样 |
| `record_initial_history_reconstructs_resumed_transcript` | 记录初始历史正确重建恢复的对话 |
| `record_initial_history_new_defers_initial_context_until_first_turn` | 新会话延迟初始上下文注入到第一个 Turn |
| `resumed_history_injects_initial_context_on_first_context_update_only` | 恢复历史仅在首次上下文更新时注入初始上下文 |
| `record_initial_history_seeds_token_info_from_rollout` | 从 rollout 种子 token 使用信息 |
| `recompute_token_usage_uses_session_base_instructions` | 重算 token 使用量使用会话基础指令 |
| `recompute_token_usage_updates_model_context_window` | 重算 token 使用量更新模型上下文窗口 |
| `record_initial_history_reconstructs_forked_transcript` | 记录初始历史正确重建 forked 对话 |
| `fork_startup_context_then_first_turn_diff_snapshot` | Fork 后首次 Turn 的上下文 diff 快照测试 |
| `record_initial_history_forked_hydrates_previous_turn_settings` | Forked 初始历史正确水化 previous_turn_settings |
| `thread_rollback_drops_last_turn_from_history` | 线程回滚丢弃最后一个 Turn |
| `thread_rollback_clears_history_when_num_turns_exceeds_existing_turns` | 回滚 Turn 数超过现有 Turn 数时清空历史 |
| `thread_rollback_fails_without_persisted_rollout_path` | 无持久化 rollout 路径时回滚失败 |
| `thread_rollback_recomputes_previous_turn_settings_and_reference_context_from_replay` | 回滚后从重放中重算 previous_turn_settings 和 reference_context |
| `thread_rollback_restores_cleared_reference_context_item_after_compaction` | 压缩后回滚恢复被清除的 reference_context_item |
| `thread_rollback_persists_marker_and_replays_cumulatively` | 回滚持久化标记并累积重放 |
| `thread_rollback_fails_when_turn_in_progress` | Turn 进行中时回滚失败 |
| `thread_rollback_fails_when_num_turns_is_zero` | num_turns 为 0 时回滚失败 |
| `set_rate_limits_retains_previous_credits` | 设置速率限制保留之前的积分信息 |
| `set_rate_limits_updates_plan_type_when_present` | 设置速率限制更新 plan_type |
| `prefers_structured_content_when_present` | MCP CallToolResult 优先使用 structured_content |
| `falls_back_to_content_when_structured_is_null` | structured_content 为 null 时回退到 content |
| `success_flag_reflects_is_error_true` | success 标志反映 is_error=true |
| `turn_context_with_model_updates_model_fields` | with_model 更新模型字段 |
| `session_configuration_apply_preserves_split_file_system_policy_on_cwd_only_update` | cwd 更新保留拆分文件系统策略 |
| `session_configuration_apply_rederives_legacy_file_system_policy_on_cwd_update` | cwd 更新重新推导旧版文件系统策略 |
| `session_update_settings_keeps_runtime_cwds_absolute` | 会话设置更新保持运行时 cwd 为绝对路径 |
| `session_new_fails_when_zsh_fork_enabled_without_zsh_path` | 启用 zsh fork 但无 zsh_path 时 Session 创建失败 |
| `abort_regular_task_emits_turn_aborted_only` | 中止普通任务仅发出 TurnAborted |
| `abort_gracefully_emits_turn_aborted_only` | 优雅中止仅发出 TurnAborted |
| `steer_input_requires_active_turn` | steer_input 需要活跃 Turn |
| `steer_input_enforces_expected_turn_id` | steer_input 强制校验 expected_turn_id |
| `steer_input_rejects_non_regular_turns` | steer_input 拒绝非普通 Turn（Review/Compact） |
| `steer_input_returns_active_turn_id` | steer_input 返回活跃 Turn ID |
| `shutdown_and_wait_allows_multiple_waiters` | shutdown_and_wait 支持多个等待者 |
| `shutdown_and_wait_shuts_down_cached_guardian_subagent` | 关闭时清理缓存的 Guardian 子代理 |
| `build_settings_update_items_emits_environment_item_for_network_changes` | 网络变更时发出环境更新项 |
| `build_settings_update_items_emits_realtime_start_when_session_becomes_live` | 会话开始实时对话时发出 realtime start |
| `build_initial_context_prepends_model_switch_message` | 模型切换时初始上下文前置 model_switch 消息 |
| `record_context_updates_and_set_reference_context_item_injects_full_context_when_baseline_missing` | 基线缺失时注入完整上下文 |
| `record_context_updates_and_set_reference_context_item_persists_baseline_without_emitting_diffs` | 无 diff 时仍持久化基线到 rollout |
| `rejects_escalated_permissions_when_policy_not_on_request` | 审批策略非 OnRequest 时拒绝提权请求 |
| `unified_exec_rejects_escalated_permissions_when_policy_not_on_request` | unified exec 同样拒绝非 OnRequest 下的提权 |
| `fatal_tool_error_stops_turn_and_reports_error` | 致命工具错误停止 Turn 并报告错误 |
| `spawn_task_turn_span_inherits_dispatch_trace_context` | spawn_task 的 Turn span 继承 dispatch 的 trace context |
| `new_default_turn_uses_config_aware_skills_for_role_overrides` | 新默认 Turn 使用配置感知的技能（角色覆盖） |
| `user_turn_updates_approvals_reviewer` | UserTurn 更新 approvals_reviewer |
| `run_user_shell_command_does_not_set_reference_context_item` | 独立 shell 命令不改变 reference_context_item |
