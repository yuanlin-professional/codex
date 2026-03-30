# `websocket_fallback.rs` -- 测试模块

## 测试覆盖范围
测试 WebSocket 到 HTTP 的回退（fallback）机制，包括收到 426 Upgrade Required 时立即切换、重试耗尽后切换、回退的粘性跨 turn 保持，以及回退过程中 StreamError 事件的抑制行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `websocket_fallback_switches_to_http_on_upgrade_required_connect` | WebSocket 预热请求收到 426 后立即切换到 HTTP，首次 turn 直接走 HTTP（仅 1 次 WS 尝试 + 1 次 HTTP） |
| `websocket_fallback_switches_to_http_after_retries_exhausted` | WebSocket 连接失败达到最大重试次数后切换到 HTTP（1 次预热 + 3 次流尝试 + 1 次 HTTP） |
| `websocket_fallback_hides_first_websocket_retry_stream_error` | 回退过程中隐藏首次 WebSocket 重试的 StreamError 事件（debug 模式下可见） |
| `websocket_fallback_is_sticky_across_turns` | 回退到 HTTP 后，后续 turn 不再尝试 WebSocket，直接走 HTTP |
