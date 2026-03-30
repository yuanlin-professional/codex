# `command_exec.rs` — 测试模块

## 测试覆盖范围
测试 `command/exec` JSON-RPC 方法的完整功能，包括进程终止、旧版缓冲兼容、
环境变量覆盖、参数验证（超时冲突、输出上限冲突、负超时）、流式输出、
管道 stdin 写入、PTY 模式、终端大小设置/调整，以及连接作用域的进程 ID 隔离。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `command_exec_without_streams_can_be_terminated` | 非流式命令可被终止 |
| `command_exec_without_process_id_keeps_buffered_compatibility` | 无 process_id 时保持旧版缓冲行为 |
| `command_exec_env_overrides_merge_with_server_environment_and_support_unset` | 环境变量覆盖、新增和移除 |
| `command_exec_rejects_disable_timeout_with_timeout_ms` | 同时设置 disableTimeout 和 timeoutMs 被拒绝 |
| `command_exec_rejects_disable_output_cap_with_output_bytes_cap` | 同时设置 disableOutputCap 和 outputBytesCap 被拒绝 |
| `command_exec_rejects_negative_timeout_ms` | 负数 timeoutMs 被拒绝 |
| `command_exec_without_process_id_rejects_streaming` | 无 process_id 时拒绝流式输出请求 |
| `command_exec_non_streaming_respects_output_cap` | 非流式模式下输出上限截断正常 |
| `command_exec_streaming_does_not_buffer_output` | 流式模式不缓冲输出，达到上限立即截断 |
| `command_exec_pipe_streams_output_and_accepts_write` | 管道模式流式输出和 stdin 写入 |
| `command_exec_tty_implies_streaming_and_reports_pty_output` | TTY 模式隐含流式传输并报告 PTY 输出 |
| `command_exec_tty_supports_initial_size_and_resize` | PTY 支持初始大小设置和运行时调整 |
| `command_exec_process_ids_are_connection_scoped_and_disconnect_terminates_process` | 进程 ID 按连接隔离，断开连接终止进程 |
