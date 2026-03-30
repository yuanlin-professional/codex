# `compact_remote.rs` — 测试模块

## 测试覆盖范围

测试远程(Remote/Realtime)模式下的上下文压缩功能，包括手动和自动远程压缩、function_call 历史裁剪以适应上下文窗口、压缩失败处理、base instructions 的 Token 估算、上下文压缩项事件、Rollout 持久化、恢复(resume)后的开发者指令刷新、Realtime 会话的 start/end 重放，以及各种请求形态的快照测试。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `remote_compact_replaces_history_for_followups` | 远程压缩替换后续请求的历史 |
| `remote_compact_runs_automatically` | 远程自动压缩在 Token 超限后运行 |
| `remote_compact_trims_function_call_history_to_fit_context_window` | 手动远程压缩裁剪 function_call 历史以适应上下文窗口 |
| `auto_remote_compact_trims_function_call_history_to_fit_context_window` | 自动远程压缩裁剪 function_call 历史 |
| `auto_remote_compact_failure_stops_agent_loop` | 自动远程压缩失败时停止代理循环 |
| `remote_compact_trim_estimate_uses_session_base_instructions` | 远程压缩裁剪估算使用会话基础指令 |
| `remote_manual_compact_emits_context_compaction_items` | 远程手动压缩产生上下文压缩项事件 |
| `remote_manual_compact_failure_emits_task_error_event` | 远程手动压缩失败时发出任务错误事件 |
| `remote_compact_persists_replacement_history_in_rollout` | 远程压缩将替换历史持久化到 Rollout |
| `remote_compact_and_resume_refresh_stale_developer_instructions` | 远程压缩后恢复时刷新过时的开发者指令 |
| `remote_compact_refreshes_stale_developer_instructions_without_resume` | 远程压缩无需恢复即可刷新过时的开发者指令 |
| `snapshot_request_shape_remote_pre_turn_compaction_restates_realtime_start` | 快照测试：远程轮前压缩重新声明 Realtime start |
| `remote_request_uses_custom_experimental_realtime_start_instructions` | 远程请求使用自定义的实验性 Realtime start 指令 |
| `snapshot_request_shape_remote_pre_turn_compaction_restates_realtime_end` | 快照测试：远程轮前压缩重新声明 Realtime end |
| `snapshot_request_shape_remote_manual_compact_restates_realtime_start` | 快照测试：远程手动压缩重新声明 Realtime start |
| `snapshot_request_shape_remote_mid_turn_compaction_does_not_restate_realtime_end` | 快照测试：远程轮中压缩不重新声明 Realtime end |
| `snapshot_request_shape_remote_compact_resume_restates_realtime_end` | 快照测试：远程压缩恢复重新声明 Realtime end |
| `snapshot_request_shape_remote_pre_turn_compaction_including_incoming_user_message` | 快照测试：包含传入用户消息的远程轮前压缩 |
| `snapshot_request_shape_remote_pre_turn_compaction_strips_incoming_model_switch` | 快照测试：远程轮前压缩去除传入的模型切换 |
| `snapshot_request_shape_remote_pre_turn_compaction_context_window_exceeded` | 快照测试：上下文窗口超出时的远程轮前压缩 |
| `snapshot_request_shape_remote_mid_turn_continuation_compaction` | 快照测试：远程轮中继续执行的压缩 |
| `snapshot_request_shape_remote_mid_turn_compaction_summary_only_reinjects_context` | 快照测试：仅摘要的远程轮中压缩重新注入上下文 |
| `snapshot_request_shape_remote_mid_turn_compaction_multi_summary_reinjects_above_last_summary` | 快照测试：多摘要的远程轮中压缩在最后摘要上方重新注入 |
| `snapshot_request_shape_remote_manual_compact_without_previous_user_messages` | 快照测试：无先前用户消息时的远程手动压缩 |
