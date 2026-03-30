# `compact.rs` — 测试模块

## 测试覆盖范围

测试上下文压缩(Compact)功能，包括手动压缩和自动压缩、自定义压缩提示词、Token 使用量事件、上下文压缩项事件、多次自动压缩、Token 限制触发、恢复(resume)后的自动压缩、模型切换时的预采样压缩、Rollout 持久化、压缩失败重试、两次连续手动压缩的消息保留，以及各种请求形态(shape)的快照测试。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `summarize_context_three_requests_and_instructions` | 基础压缩流程：三次请求中验证摘要上下文和指令 |
| `manual_compact_uses_custom_prompt` | 手动压缩使用自定义压缩提示词 |
| `manual_compact_emits_api_and_local_token_usage_events` | 手动压缩触发 API 和本地 Token 使用量事件 |
| `manual_compact_emits_context_compaction_items` | 手动压缩产生上下文压缩项事件 |
| `multiple_auto_compact_per_task_runs_after_token_limit_hit` | 单任务中多次自动压缩在 Token 限制命中后运行 |
| `auto_compact_runs_after_token_limit_hit` | Token 限制命中后触发自动压缩 |
| `auto_compact_emits_context_compaction_items` | 自动压缩产生上下文压缩项事件 |
| `auto_compact_starts_after_turn_started` | 自动压缩在轮次开始后启动 |
| `auto_compact_runs_after_resume_when_token_usage_is_over_limit` | 恢复会话后 Token 超限时运行自动压缩 |
| `pre_sampling_compact_runs_on_switch_to_smaller_context_model` | 切换到更小上下文窗口的模型时运行预采样压缩 |
| `pre_sampling_compact_runs_after_resume_and_switch_to_smaller_model` | 恢复并切换到更小模型后运行预采样压缩 |
| `auto_compact_persists_rollout_entries` | 自动压缩结果持久化到 Rollout 条目 |
| `manual_compact_retries_after_context_window_error` | 上下文窗口错误后手动压缩自动重试 |
| `manual_compact_non_context_failure_retries_then_emits_task_error` | 非上下文错误时重试后发出任务错误事件 |
| `manual_compact_twice_preserves_latest_user_messages` | 两次手动压缩保留最新的用户消息 |
| `auto_compact_allows_multiple_attempts_when_interleaved_with_other_turn_events` | 与其他轮次事件交错时允许多次自动压缩尝试 |
| `snapshot_request_shape_mid_turn_continuation_compaction` | 快照测试：轮次中继续执行的压缩请求形态 |
| `auto_compact_clamps_config_limit_to_context_window` | 自动压缩将配置限制钳位到上下文窗口大小 |
| `auto_compact_counts_encrypted_reasoning_before_last_user` | 自动压缩计算最后一条用户消息前的加密推理 Token |
| `auto_compact_runs_when_reasoning_header_clears_between_turns` | 轮次间推理头清除时运行自动压缩 |
| `snapshot_request_shape_pre_turn_compaction_including_incoming_user_message` | 快照测试：包含传入用户消息的轮前压缩 |
| `snapshot_request_shape_pre_turn_compaction_strips_incoming_model_switch` | 快照测试：轮前压缩去除传入的模型切换 |
| `snapshot_request_shape_pre_turn_compaction_context_window_exceeded` | 快照测试：上下文窗口超出时的轮前压缩 |
| `snapshot_request_shape_manual_compact_without_previous_user_messages` | 快照测试：无先前用户消息时的手动压缩 |
