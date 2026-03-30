# `agent_jobs.rs` — 测试模块

## 测试覆盖范围

测试代理任务(Agent Jobs)功能，包括通过 CSV 批量生成代理任务、任务结果报告、线程安全校验、Item ID 去重以及提前停止(stop)机制。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `report_agent_job_result_rejects_wrong_thread` | 使用错误的线程 ID 报告代理任务结果时，应被拒绝（返回 false） |
| `spawn_agents_on_csv_runs_and_exports` | 从 CSV 文件生成代理任务，执行完毕后验证输出 CSV 包含 result_json 和 item_id 列 |
| `spawn_agents_on_csv_dedupes_item_ids` | 当 CSV 中存在重复的 ID 列值时，系统自动去重生成唯一 item_id（如 foo 和 foo-2） |
| `spawn_agents_on_csv_stop_halts_future_items` | 子代理返回 stop=true 后，剩余待处理项应被取消，任务状态变为 Cancelled |
