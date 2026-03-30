# `transport_tests.rs` — 测试模块

## 测试覆盖范围

验证 `parse_listen_url` 对各种 URL 格式的解析行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `parse_listen_url_accepts_default_websocket_url` | 默认 `ws://127.0.0.1:0` 正确解析 |
| `parse_listen_url_accepts_websocket_url` | 指定端口 `ws://127.0.0.1:1234` 正确解析 |
| `parse_listen_url_rejects_invalid_websocket_url` | 主机名 `ws://localhost:1234` 被拒绝 |
| `parse_listen_url_rejects_unsupported_url` | HTTP scheme 被拒绝 |
