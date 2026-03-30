# `server_error_exit.rs` — 测试模块

## 测试覆盖范围

验证服务端报告错误时 codex-exec 的退出状态。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `exits_non_zero_when_server_reports_error` | 服务端返回 response.failed 事件时以退出码 1 退出 |
