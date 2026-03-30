# `request_user_input_tool_tests.rs` -- 测试模块

## 测试覆盖范围
验证 request_user_input 工具定义的参数结构和 JSON 序列化。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `request_user_input_tool_serializes` | 验证工具 JSON 序列化包含 question 和 options 参数 |
| `request_user_input_options_structure` | 验证 options 数组项包含 label 和 description 属性 |
