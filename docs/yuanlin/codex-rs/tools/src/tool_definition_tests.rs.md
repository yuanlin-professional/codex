# `tool_definition_tests.rs` -- 测试模块

## 测试覆盖范围
验证 ToolDefinition 的辅助方法（renamed、into_deferred）。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `renamed_changes_name` | 验证 renamed 方法正确修改工具名称 |
| `into_deferred_clears_output_schema` | 验证 into_deferred 清除 output_schema 并设置 defer_loading |
