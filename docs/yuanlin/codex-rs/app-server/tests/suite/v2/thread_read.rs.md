# `thread_read.rs` — 测试模块

## 测试覆盖范围
测试 `thread/read` JSON-RPC 方法，验证摘要返回、包含 turn 历史、物化前的预计算路径、
名称设置后在 read/list/resume 中的反映、未物化线程拒绝包含 turn 请求，
以及失败 turn 后的系统错误标志。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_read_returns_summary_without_turns` | 返回摘要但不含 turns |
| `thread_read_can_include_turns` | 可选包含 turn 历史 |
| `thread_read_loaded_thread_returns_precomputed_path_before_materialization` | 物化前返回预计算路径 |
| `thread_name_set_is_reflected_in_read_list_and_resume` | 名称设置后在各接口中反映 |
| `thread_read_include_turns_rejects_unmaterialized_loaded_thread` | 未物化线程拒绝包含 turns |
| `thread_read_reports_system_error_idle_flag_after_failed_turn` | 失败 turn 后报告系统错误标志 |
