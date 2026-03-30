# `exec_process.rs` — 测试模块

## 测试覆盖范围

通过 `ExecBackend` trait 接口测试本地和远程进程执行功能，使用 `test_case` 参数化 `local` 和 `remote` 两种模式。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `exec_process_starts_and_exits` | 进程启动并正常退出（local/remote） |
| `exec_process_streams_output` | 进程输出流式读取（local/remote） |
| `exec_process_write_then_read` | PTY 进程 stdin 写入后读取输出（local/remote） |
| `exec_process_preserves_queued_events_before_subscribe` | 在订阅前产生的输出可在之后读取（local/remote） |
| `remote_exec_process_reports_transport_disconnect` | 远程服务器关闭后进程会话报告失败 |
