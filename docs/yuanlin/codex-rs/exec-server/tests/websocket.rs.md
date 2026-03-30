# `websocket.rs` — 测试模块

## 测试覆盖范围

验证 exec-server 对格式错误的 WebSocket 消息的容错处理。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `exec_server_reports_malformed_websocket_json_and_keeps_running` | 发送非 JSON 文本后收到错误响应，但连接不断开，后续 initialize 正常工作 |
