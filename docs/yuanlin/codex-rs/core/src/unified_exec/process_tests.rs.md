# `process_tests.rs` — 测试模块

## 测试覆盖范围

测试 `process.rs` 中 `UnifiedExecProcess` 的远程进程行为，使用 `MockExecProcess` 模拟远程 exec-server 进程，验证写入失败处理和早期退出检测。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `remote_write_unknown_process_marks_process_exited` | 验证远程写入返回 `UnknownProcess` 状态时，进程被标记为已退出 |
| `remote_write_closed_stdin_marks_process_exited` | 验证远程写入返回 `StdinClosed` 状态时，进程被标记为已退出 |
| `remote_process_waits_for_early_exit_event` | 验证远程进程在早期退出宽限期内正确检测退出事件，并获取退出码 |
