# `process_manager_tests.rs` — 测试模块

## 测试覆盖范围

测试 `process_manager.rs` 中的环境变量注入、进程 ID 转换以及进程淘汰策略的正确性。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `unified_exec_env_injects_defaults` | 验证 `apply_unified_exec_env` 在空环境上正确注入 10 个默认环境变量 |
| `unified_exec_env_overrides_existing_values` | 验证环境变量注入会覆盖已有值（如 `NO_COLOR`），但保留未冲突的值（如 `PATH`） |
| `exec_server_process_id_matches_unified_exec_process_id` | 验证 `exec_server_process_id` 将 i32 正确转为字符串 |
| `pruning_prefers_exited_processes_outside_recently_used` | 验证淘汰策略优先选择已退出且不在最近 8 个之内的进程 |
| `pruning_falls_back_to_lru_when_no_exited` | 验证无已退出进程时，淘汰最久未使用（LRU）的进程 |
| `pruning_protects_recent_processes_even_if_exited` | 验证即使进程已退出，若在最近 8 个之内则受保护，淘汰更旧的存活进程 |
