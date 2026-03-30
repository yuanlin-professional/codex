# `turn_state.rs` -- 测试模块

## 测试覆盖范围
测试 turn state（轮次状态）在 Responses API 和 WebSocket 传输中的持久化与重置行为，确保 turn state 在同一轮次内的后续请求中传递，并在新轮次开始时清除。同时验证 turn metadata 中 turn_id 的正确性。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `responses_turn_state_persists_within_turn_and_resets_after` | HTTP SSE 模式下，首次请求无 turn state header，工具调用后续请求携带 turn state，新 turn 清除 turn state；同时验证 turn_id 在同一 turn 内一致，跨 turn 不同 |
| `websocket_turn_state_persists_within_turn_and_resets_after` | WebSocket 模式下，验证相同的 turn state 持久化和重置行为 |
