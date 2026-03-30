# `realtime_websocket_e2e.rs` — 测试模块

## 测试覆盖范围

使用本地 WebSocket 服务器验证 Realtime WebSocket 客户端的端到端通信行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `realtime_ws_e2e_session_create_and_event_flow` | V1 连接建立、音频发送和接收 |
| `realtime_ws_e2e_send_while_next_event_waits` | 发送不阻塞事件接收（全双工验证） |
| `realtime_ws_e2e_disconnected_emitted_once` | 服务器关闭后多次调用 next_event 均返回 None |
| `realtime_ws_e2e_ignores_unknown_text_events` | 未知事件被跳过，后续事件正常接收 |
| `realtime_ws_e2e_realtime_v2_parser_emits_handoff_requested` | V2 解析器正确识别 codex 工具调用为 handoff |
