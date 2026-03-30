# `registry_tests.rs` — 测试模块

## 测试覆盖范围

覆盖 `AgentRegistry` 和 `SpawnReservation` 的核心功能，包括：昵称格式化与序数词后缀、session 深度计算、spawn 深度限制、预留/提交/释放流程、昵称池耗尽重置、路径预留与释放等。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `format_agent_nickname_adds_ordinals_after_reset` | 验证昵称序数词后缀的正确性（2nd、3rd、11th、21st） |
| `session_depth_defaults_to_zero_for_root_sources` | 根 session 来源（如 Cli）的深度默认为 0 |
| `thread_spawn_depth_increments_and_enforces_limit` | spawn 深度应正确递增，且超限时应被检测 |
| `non_thread_spawn_subagents_default_to_depth_zero` | 非 ThreadSpawn 类型的子 agent 深度默认为 0 |
| `reservation_drop_releases_slot` | SpawnReservation drop 时应自动释放槽位 |
| `commit_holds_slot_until_release` | commit 后槽位应保持占用直到显式释放 |
| `release_ignores_unknown_thread_id` | 释放未知 ThreadId 不应影响已有槽位 |
| `release_is_idempotent_for_registered_threads` | 对同一 ThreadId 重复释放应是幂等的 |
| `failed_spawn_keeps_nickname_marked_used` | spawn 失败时已预留的昵称仍标记为已使用 |
| `agent_nickname_resets_used_pool_when_exhausted` | 昵称池耗尽时应重置并使用序号后缀 |
| `released_nickname_stays_used_until_pool_reset` | 释放的昵称在池重置前仍标记为已使用 |
| `repeated_resets_advance_the_ordinal_suffix` | 多次重置应递增序号后缀（2nd -> 3rd） |
| `register_root_thread_indexes_root_path` | 注册根线程后应能通过根路径查找 |
| `reserved_agent_path_is_released_when_spawn_fails` | spawn 失败时预留的路径应被释放 |
| `committed_agent_path_is_indexed_until_release` | 提交的路径在释放前应保持索引 |
