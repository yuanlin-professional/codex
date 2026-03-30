# `agent_job_tool_tests.rs` -- 测试模块

## 测试覆盖范围
验证 spawn_agents_on_csv 和 report_agent_job_result 工具定义的 JSON 序列化格式和参数完整性。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `spawn_agents_on_csv_tool_serializes` | 验证 CSV 批处理工具的 JSON 序列化包含所有参数 |
| `report_agent_job_result_tool_serializes` | 验证结果报告工具的 JSON 序列化 |
| `spawn_agents_on_csv_required_fields` | 验证必填字段为 csv_path 和 instruction |
| `report_agent_job_result_required_fields` | 验证必填字段为 job_id、item_id 和 result |
