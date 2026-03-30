# `dynamic_tool_tests.rs` -- 测试模块

## 测试覆盖范围
验证动态工具（DynamicToolSpec）到 ToolDefinition 的解析逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `parse_dynamic_tool_basic` | 解析基本的动态工具定义，验证名称、描述和 input_schema |
| `parse_dynamic_tool_deferred` | 验证 defer_loading 标志正确传递 |
