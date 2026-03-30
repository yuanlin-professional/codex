# `connection_handling_websocket_unix.rs` — 测试模块

## 测试覆盖范围
测试 Unix 平台上 WebSocket 服务器的优雅关闭行为，包括 SIGINT (Ctrl-C) 和 SIGTERM 信号处理，
验证服务器在 turn 运行中收到信号后等待完成再退出，以及第二次信号强制退出。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `websocket_transport_ctrl_c_waits_for_running_turn_before_exit` | Ctrl-C 时等待运行中的 turn 完成后退出 |
| `websocket_transport_second_ctrl_c_forces_exit_while_turn_running` | 第二次 Ctrl-C 强制退出 |
| `websocket_transport_sigterm_waits_for_running_turn_before_exit` | SIGTERM 时等待运行中的 turn 完成后退出 |
| `websocket_transport_second_sigterm_forces_exit_while_turn_running` | 第二次 SIGTERM 强制退出 |
