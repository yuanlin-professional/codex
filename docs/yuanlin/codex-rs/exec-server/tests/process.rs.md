# `process.rs` — 测试模块

## 测试覆盖范围

验证通过 WebSocket JSON-RPC 启动进程的端到端流程。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `exec_server_starts_process_over_websocket` | 完成初始化后发送 process/start 并验证响应 |
