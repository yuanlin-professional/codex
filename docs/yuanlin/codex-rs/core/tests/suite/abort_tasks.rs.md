# `abort_tasks.rs` — 测试模块

## 测试覆盖范围

测试任务中断(Interrupt)和中止(Abort)相关逻辑，包括中断长时间运行的工具调用、中断后历史记录的正确性，以及中断标记在后续请求中的持久化。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `interrupt_long_running_tool_emits_turn_aborted` | 中断一个长时间运行的 shell_command 工具调用后，期望收到 TurnAborted 事件 |
| `interrupt_tool_records_history_entries` | 中断工具调用后，后续请求中应包含原始工具调用和合成的 "aborted" function_call_output，并验证经过时间格式 |
| `interrupt_persists_turn_aborted_marker_in_next_request` | 中断后在对话历史中持久化 `<turn_aborted>` 标记，并验证该标记出现在后续 `/responses` 请求中 |
