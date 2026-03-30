# `control_tests.rs` — 测试模块

## 测试覆盖范围

覆盖 `AgentControl` 的完整功能，包括：状态管理、输入发送、agent 创建与恢复、线程限制、子 agent 完成通知、agent 间通信、昵称分配、树形关闭、rollout 恢复等多 agent 控制平面的核心行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `send_input_errors_when_manager_dropped` | 当 ThreadManager 已释放时，send_input 应返回错误 |
| `get_status_returns_not_found_without_manager` | 无 manager 时 get_status 返回 NotFound |
| `on_event_updates_status_from_task_started` | TurnStarted 事件将状态更新为 Running |
| `on_event_updates_status_from_task_complete` | TurnComplete 事件将状态更新为 Completed |
| `on_event_updates_status_from_error` | Error 事件将状态更新为 Errored |
| `on_event_updates_status_from_turn_aborted` | TurnAborted(Interrupted) 将状态更新为 Interrupted |
| `on_event_updates_status_from_shutdown_complete` | ShutdownComplete 将状态更新为 Shutdown |
| `spawn_agent_errors_when_manager_dropped` | 无 manager 时 spawn_agent 应返回错误 |
| `resume_agent_errors_when_manager_dropped` | 无 manager 时 resume_agent 应返回错误 |
| `send_input_errors_when_thread_missing` | 向不存在的线程发送输入应返回 ThreadNotFound |
| `get_status_returns_not_found_for_missing_thread` | 不存在的线程状态应为 NotFound |
| `get_status_returns_pending_init_for_new_thread` | 新建线程的初始状态应为 PendingInit |
| `subscribe_status_errors_for_missing_thread` | 订阅不存在线程的状态应失败 |
| `subscribe_status_updates_on_shutdown` | 关闭线程时状态订阅应收到 Shutdown 更新 |
| `send_input_submits_user_message` | send_input 应正确提交用户消息 |
| `send_inter_agent_communication_without_turn_queues_message_without_triggering_turn` | 非触发轮次的 agent 间通信应排队而不立即执行 |
| `append_message_records_assistant_message` | append_message 应正确记录助手消息到历史 |
| `spawn_agent_creates_thread_and_sends_prompt` | spawn_agent 应创建线程并发送初始提示词 |
| `spawn_agent_can_fork_parent_thread_history` | fork 模式应正确复制父线程历史到子线程 |
| `spawn_agent_fork_injects_output_for_parent_spawn_call` | fork 应为父 spawn 调用注入合成的 tool output |
| `spawn_agent_fork_flushes_parent_rollout_before_loading_history` | fork 前应先刷新父线程 rollout |
| `spawn_agent_respects_max_threads_limit` | 应遵守最大线程数限制 |
| `spawn_agent_releases_slot_after_shutdown` | 关闭 agent 后应释放线程槽位 |
| `spawn_agent_limit_shared_across_clones` | 克隆的 AgentControl 应共享线程限制 |
| `resume_agent_respects_max_threads_limit` | 恢复 agent 时应遵守最大线程数限制 |
| `resume_agent_releases_slot_after_resume_failure` | 恢复失败时应释放已占用的槽位 |
| `spawn_child_completion_notifies_parent_history` | 子 agent 完成时应通知父线程历史 |
| `multi_agent_v2_completion_ignores_dead_direct_parent` | V2 模式下子 agent 完成时忽略已关闭的直接父 agent |
| `multi_agent_v2_completion_queues_message_for_direct_parent` | V2 模式下子 agent 完成应向直接父 agent 发送通信 |
| `completion_watcher_notifies_parent_when_child_is_missing` | 子 agent 不存在时完成监视器仍应通知父线程 |
| `spawn_thread_subagent_gets_random_nickname_in_session_source` | 子 agent spawn 时应获得随机昵称 |
| `spawn_thread_subagent_uses_role_specific_nickname_candidates` | 应使用角色特定的昵称候选列表 |
| `resume_thread_subagent_restores_stored_nickname_and_role` | 恢复的子 agent 应保留原始昵称和角色 |
| `resume_agent_from_rollout_reads_archived_rollout_path` | 应能从归档路径恢复 agent |
| `shutdown_agent_tree_closes_live_descendants` | 树形关闭应关闭所有活跃后代 |
| `shutdown_agent_tree_closes_descendants_when_started_at_child` | 从子节点开始的树形关闭也应关闭其后代 |
| `resume_agent_from_rollout_does_not_reopen_closed_descendants` | 恢复时不应重新打开已关闭的后代 |
| `resume_closed_child_reopens_open_descendants` | 恢复已关闭的子 agent 应重新打开其开放后代 |
| `resume_agent_from_rollout_reopens_open_descendants_after_manager_shutdown` | manager 关闭后恢复应重建完整的后代树 |
| `resume_agent_from_rollout_uses_edge_data_when_descendant_metadata_source_is_stale` | 元数据过时时应使用边数据恢复后代 |
| `resume_agent_from_rollout_skips_descendants_when_parent_resume_fails` | 父 agent 恢复失败时应跳过后代恢复 |
