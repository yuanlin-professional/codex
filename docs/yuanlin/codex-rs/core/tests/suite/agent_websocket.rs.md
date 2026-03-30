# `agent_websocket.rs` — 测试模块

## 测试覆盖范围

测试 Codex 代理通过 WebSocket 协议与模型进行交互的功能，包括 shell 命令链式调用、启动预热(prewarm)与连接复用、WebSocket V2 协议支持，以及服务层级(service tier)的动态切换。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `websocket_test_codex_shell_chain` | 通过 WebSocket 执行 shell 命令链，验证两个请求均为 response.create 类型且包含正确输入 |
| `websocket_first_turn_uses_startup_prewarm_and_create` | 首轮交互使用启动预热(generate=false)后再发送实际请求，验证单次握手和两条请求 |
| `websocket_first_turn_handles_handshake_delay_with_startup_prewarm` | 模拟握手延迟(150ms)下，首轮仍能正确完成预热和请求 |
| `websocket_v2_test_codex_shell_chain` | 启用 V2 协议后执行 shell 命令链，验证三条请求链和 previous_response_id 的正确传递 |
| `websocket_v2_first_turn_uses_updated_fast_tier_after_startup_prewarm` | V2 协议下预热后动态切换为 Fast 服务层级，验证 service_tier 为 "priority" |
| `websocket_v2_first_turn_drops_fast_tier_after_startup_prewarm` | V2 协议下初始配置 Fast 层级但实际请求不指定时，验证 service_tier 被正确移除 |
| `websocket_v2_next_turn_uses_updated_service_tier` | 跨轮次动态更新服务层级，验证第一轮为 priority、第二轮无 service_tier |
