# `connection_handling_websocket.rs` — 测试模块

## 测试覆盖范围
测试 WebSocket 传输层的核心功能，包括每连接独立握手和响应路由、健康检查端点、
浏览器 Origin 头拒绝策略、能力令牌（capability-token）认证、HMAC-SHA256 签名短期令牌认证，
以及非回环地址的默认认证行为。同时提供大量可复用的 WebSocket 测试辅助函数。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `websocket_transport_routes_per_connection_handshake_and_responses` | 每个连接独立握手和响应路由 |
| `websocket_transport_serves_health_endpoints_on_same_listener` | 同一监听器上提供 /readyz 和 /healthz |
| `websocket_transport_rejects_browser_origin_without_auth` | 带浏览器 Origin 头但无认证时拒绝连接 |
| `websocket_transport_rejects_missing_and_invalid_capability_tokens` | 缺少或无效的能力令牌被拒绝 |
| `websocket_transport_verifies_signed_short_lived_bearer_tokens` | 验证 HMAC-SHA256 签名令牌（过期/未生效/错误签发者/错误受众/错误签名） |
| `websocket_transport_rejects_short_signed_bearer_secret_configuration` | 过短的签名密钥导致启动失败 |
| `websocket_transport_allows_unauthenticated_non_loopback_startup_by_default` | 默认允许非回环地址无认证连接 |
