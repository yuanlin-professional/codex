# `thread_manager_tests.rs` — 测试模块

## 测试覆盖范围

线程管理器（ThreadManager），包括对话历史截断、快照状态检测、中断快照边界追加、线程分叉与恢复、多线程关闭、模型刷新提供者配置。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `truncates_before_requested_user_message` | 在请求的第 n 个用户消息之前截断历史 |
| `out_of_range_truncation_drops_only_unfinished_suffix_mid_turn` | 超出范围截断仅丢弃未完成的中间轮次后缀 |
| `fork_thread_accepts_legacy_usize_snapshot_argument` | fork_thread 接受传统 usize 快照参数 |
| `out_of_range_truncation_drops_pre_user_active_turn_prefix` | 超出范围截断丢弃活跃轮次用户消息前的前缀 |
| `ignores_session_prefix_messages_when_truncating` | 截断时忽略会话前缀消息 |
| `shutdown_all_threads_bounded_submits_shutdown_to_every_thread` | 有界关闭向每个线程提交关闭请求 |
| `new_uses_configured_openai_provider_for_model_refresh` | 新建线程管理器使用配置的 OpenAI 提供者进行模型刷新 |
| `interrupted_fork_snapshot_appends_interrupt_boundary` | 中断分叉快照追加中断边界标记 |
| `interrupted_snapshot_is_not_mid_turn` | 中断快照不被视为轮次中间状态 |
| `completed_legacy_event_history_is_not_mid_turn` | 完成的传统事件历史不被视为轮次中间状态 |
| `mixed_response_and_legacy_user_event_history_is_mid_turn` | 混合响应和传统用户事件历史被视为轮次中间状态 |
| `interrupted_fork_snapshot_does_not_synthesize_turn_id_for_legacy_history` | 传统历史的中断分叉快照不合成 turn_id |
| `interrupted_fork_snapshot_preserves_explicit_turn_id` | 中断分叉快照保留显式 turn_id |
| `interrupted_fork_snapshot_uses_persisted_mid_turn_history_without_live_source` | 无活跃源时中断分叉快照使用持久化的中间轮次历史 |
