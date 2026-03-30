# `tests.rs` — 测试模块

## 测试覆盖范围

全面测试 PTY 和管道进程生成的功能，包括输出收集、stdin 往返、会话分离、终止行为、文件描述符保留和 PTY 大小调整。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `pty_python_repl_emits_output_and_exits` | PTY 模式下 Python REPL 正确输出并退出 |
| `pipe_process_round_trips_stdin` | 管道进程 stdin 往返测试 |
| `pipe_process_detaches_from_parent_session` | 管道进程脱离父会话（Unix） |
| `pipe_and_pty_share_interface` | 管道和 PTY 共享统一接口 |
| `pipe_drains_stderr_without_stdout_activity` | 无 stdout 时正确排空 stderr |
| `pipe_process_can_expose_split_stdout_and_stderr` | 分离 stdout/stderr 输出 |
| `pipe_terminate_aborts_detached_readers` | 终止后中止分离的读取器 |
| `pty_terminate_kills_background_children_in_same_process_group` | PTY 终止杀死同进程组的后台子进程 |
| `pty_spawn_can_preserve_inherited_fds` | PTY 生成保留继承文件描述符 |
| `pty_preserving_inherited_fds_keeps_python_repl_running` | 保留 fd 不影响 Python REPL 运行 |
| `pty_spawn_with_inherited_fds_reports_exec_failures` | 不存在的可执行文件正确报告错误 |
| `pty_spawn_with_inherited_fds_supports_resize` | 带保留 fd 的 PTY 支持大小调整 |
| `pipe_spawn_no_stdin_can_preserve_inherited_fds` | 无 stdin 管道生成保留继承 fd |
