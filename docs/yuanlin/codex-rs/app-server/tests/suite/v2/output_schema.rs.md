# `output_schema.rs` — 测试模块

## 测试覆盖范围
测试 turn 启动时的输出 schema（output_schema）功能，验证 v2 协议下 JSON schema 约束的传递以及每轮独立性。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `turn_start_accepts_output_schema_v2` | turn/start 接受 output_schema 参数 |
| `turn_start_output_schema_is_per_turn_v2` | output_schema 为每轮独立设置 |
