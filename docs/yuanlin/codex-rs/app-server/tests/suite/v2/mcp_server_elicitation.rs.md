# `mcp_server_elicitation.rs` — 测试模块

## 测试覆盖范围
测试 MCP 服务器引导（elicitation）的端到端往返流程，验证客户端通过 app-server
与 MCP 服务器之间的工具调用和引导交互能正确传递。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `mcp_server_elicitation_round_trip` | MCP 服务器引导请求的完整往返流程 |
