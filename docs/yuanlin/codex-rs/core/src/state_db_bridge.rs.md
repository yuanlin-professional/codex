# `state_db_bridge.rs` — 状态数据库桥接层

该文件是 `codex_rollout::state_db` 模块的薄桥接层，将底层状态数据库的公共 API 重新导出供 `codex_core` 内部使用。重新导出的内容包括 `StateDbHandle`、`apply_rollout_items`、`find_rollout_path_by_id`、`get_dynamic_tools`、`list_thread_ids_db`、`list_threads_db`、`mark_thread_memory_mode_polluted`、`normalize_cwd_for_state_db`、`open_if_present`、`persist_dynamic_tools`、`read_repair_rollout_path`、`reconcile_rollout`、`touch_thread_updated_at` 等函数，以及 `codex_state::LogEntry`。同时提供 `get_state_db` 异步函数，从 `Config` 获取状态数据库句柄。
