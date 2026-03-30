# `tests.rs` — handler 单元测试模块

## 测试覆盖范围

验证 `ExecServerHandler` 的进程管理逻辑，包括并发启动重复进程 ID 和终止已退出进程的行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `duplicate_process_ids_allow_only_one_successful_start` | 并发启动相同 process_id 时仅有一个成功，另一个返回 -32600 错误 |
| `terminate_reports_false_after_process_exit` | 进程退出后 terminate 返回 running: false |
