# `memories.rs` — 测试模块

## 测试覆盖范围
记忆（Memories）系统的阶段二（Phase 2）处理，包括跨运行的输入增删追踪、Web 搜索污染检测等。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `memories_startup_phase2_tracks_added_and_removed_inputs_across_runs` | Phase 2 启动时正确追踪跨运行的新增和移除输入，并验证原始记忆和 rollout 摘要的持久化 |
| `web_search_pollution_moves_selected_thread_into_removed_phase2_inputs` | Web 搜索操作将线程标记为"受污染"，将其从已选 Phase 2 输入移至已移除列表 |
