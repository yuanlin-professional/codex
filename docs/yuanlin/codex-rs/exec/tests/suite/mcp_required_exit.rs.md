# `mcp_required_exit.rs` — 测试模块

## 测试覆盖范围

验证必需的 MCP 服务器初始化失败时的退出行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `exits_non_zero_when_required_mcp_server_fails_to_initialize` | 配置 required=true 的 MCP 服务器启动失败时以退出码 1 退出 |
