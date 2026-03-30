# `tool_parallelism.rs` -- 测试模块

## 测试覆盖范围
测试工具调用的并行执行能力，包括自定义同步工具、shell 命令和混合工具类型的并行执行，以及工具结果的分组顺序和流延迟下的提前执行行为。仅在非 Windows 系统上运行。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `read_file_tools_run_in_parallel` | 两个 test_sync_tool 调用通过 barrier 同步验证并行执行，总耗时远小于串行时间 |
| `shell_tools_run_in_parallel` | 两个 shell_command（sleep 0.25）并行执行，总耗时小于 1.6 秒 |
| `mixed_parallel_tools_run_in_parallel` | test_sync_tool 和 shell_command 混合并行执行 |
| `tool_results_grouped` | 三个并行 shell_command 的 function_call 在 input 中排在 function_call_output 之前，且 call_id 顺序匹配 |
| `shell_tools_start_before_response_completed_when_stream_delayed` | 流式响应中 response.completed 延迟到达时，shell 命令在收到工具调用事件后立即开始执行（不等待 completed） |
