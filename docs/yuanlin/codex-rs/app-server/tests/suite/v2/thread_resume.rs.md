# `thread_resume.rs` — 测试模块

## 测试覆盖范围
全面测试 `thread/resume` JSON-RPC 方法，涵盖未物化线程拒绝、rollout 历史返回、
持久化 git 元数据优先、不完整 turn 中断、无覆盖时不改变 updated_at、
运行中线程的流式重连、运行中拒绝历史/路径不匹配、审批请求重放、
MCP 服务器初始化失败、云需求加载错误、路径优先于 thread_id、
历史和覆盖组合，以及人格覆盖。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_resume_rejects_unmaterialized_thread` | 拒绝未物化线程的恢复 |
| `thread_resume_returns_rollout_history` | 返回 rollout 历史 |
| `thread_resume_prefers_persisted_git_metadata_for_local_threads` | 本地线程优先使用持久化的 git 元数据 |
| `thread_resume_and_read_interrupt_incomplete_rollout_turn_when_thread_is_idle` | 空闲线程中断不完整的 rollout turn |
| `thread_resume_without_overrides_does_not_change_updated_at_or_mtime` | 无覆盖时不更改 updated_at |
| `thread_resume_keeps_in_flight_turn_streaming` | 重连运行中的 turn 继续流式传输 |
| `thread_resume_rejects_history_when_thread_is_running` | 运行中拒绝传入历史 |
| `thread_resume_rejects_mismatched_path_when_thread_is_running` | 运行中拒绝不匹配的路径 |
| `thread_resume_rejoins_running_thread_even_with_override_mismatch` | 即使覆盖不匹配也能重连运行中线程 |
| `thread_resume_replays_pending_command_execution_request_approval` | 重放待处理的命令执行审批请求 |
| `thread_resume_replays_pending_file_change_request_approval` | 重放待处理的文件变更审批请求 |
| `thread_resume_with_overrides_defers_updated_at_until_turn_start` | 带覆盖的恢复延迟 updated_at 到 turn 开始 |
| `thread_resume_fails_when_required_mcp_server_fails_to_initialize` | 必需的 MCP 服务器初始化失败时恢复失败 |
| `thread_resume_surfaces_cloud_requirements_load_errors` | 云需求加载错误被表面化 |
| `thread_resume_prefers_path_over_thread_id` | 路径参数优先于 thread_id |
| `thread_resume_supports_history_and_overrides` | 支持历史和覆盖参数组合 |
| `thread_resume_accepts_personality_override` | 接受人格覆盖参数 |
