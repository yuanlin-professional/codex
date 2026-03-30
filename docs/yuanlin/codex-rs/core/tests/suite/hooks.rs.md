# `hooks.rs` — 测试模块

## 测试覆盖范围
Codex 钩子（Hooks）系统，包括 Stop 钩子、SessionStart 钩子、UserPromptSubmit 钩子、PreToolUse 钩子和 PostToolUse 钩子的阻止、继续、上下文注入等行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `stop_hook_can_block_multiple_times_in_same_turn` | Stop 钩子在同一轮对话中可以多次阻止并产生续接提示 |
| `session_start_hook_sees_materialized_transcript_path` | SessionStart 钩子能接收到已物化的 transcript 路径 |
| `resumed_thread_keeps_stop_continuation_prompt_in_history` | 恢复线程后保留之前 Stop 钩子的续接提示历史 |
| `multiple_blocking_stop_hooks_persist_multiple_hook_prompt_fragments` | 多个并行阻止型 Stop 钩子持久化多个钩子提示片段 |
| `blocked_user_prompt_submit_persists_additional_context_for_next_turn` | UserPromptSubmit 钩子阻止后将附加上下文持久化到下一轮 |
| `blocked_queued_prompt_does_not_strand_earlier_accepted_prompt` | 被阻止的排队提示不会阻塞之前已接受的提示 |
| `pre_tool_use_blocks_shell_command_before_execution` | PreToolUse 钩子通过 JSON deny 阻止 shell_command 执行 |
| `pre_tool_use_blocks_local_shell_before_execution` | PreToolUse 钩子阻止 local_shell 命令执行 |
| `pre_tool_use_blocks_exec_command_before_execution` | PreToolUse 钩子通过退出码 2 阻止 exec_command 执行 |
| `pre_tool_use_does_not_fire_for_non_shell_tools` | PreToolUse 钩子不会对非 Shell 工具（如 update_plan）触发 |
| `post_tool_use_records_additional_context_for_shell_command` | PostToolUse 钩子为 shell_command 记录附加上下文 |
| `post_tool_use_block_decision_replaces_shell_command_output_with_reason` | PostToolUse 钩子的 decision_block 模式替换命令输出为阻止原因 |
| `post_tool_use_continue_false_replaces_shell_command_output_with_stop_reason` | PostToolUse 钩子的 continue_false 模式替换命令输出为停止原因 |
| `post_tool_use_records_additional_context_for_local_shell` | PostToolUse 钩子为 local_shell 命令记录附加上下文 |
| `post_tool_use_exit_two_replaces_one_shot_exec_command_output_with_feedback` | PostToolUse 钩子退出码 2 替换 exec_command 的输出 |
| `post_tool_use_does_not_fire_for_non_shell_tools` | PostToolUse 钩子不会对非 Shell 工具触发 |
