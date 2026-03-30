# `agent_tool_tests.rs` -- 测试模块

## 测试覆盖范围
验证 agent 管理工具（spawn/wait/send/close/list/resume/assign_task）的 ToolSpec 定义、JSON 序列化和参数完整性。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `spawn_agent_v1_serializes` | v1 spawn agent 工具的 JSON 序列化 |
| `spawn_agent_v2_serializes` | v2 spawn agent 工具的 JSON 序列化 |
| `wait_agent_v1_serializes` | v1 wait agent 工具序列化 |
| `wait_agent_v2_serializes` | v2 wait agent 工具序列化 |
| `send_message_serializes` | send_message 工具序列化 |
| `list_agents_serializes` | list_agents 工具序列化 |
| `close_agent_v1_serializes` | v1 close agent 工具序列化 |
| `assign_task_serializes` | assign_task 工具序列化 |
