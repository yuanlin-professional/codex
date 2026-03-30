# `utility_tool_tests.rs` -- 测试模块

## 测试覆盖范围
验证 list_dir 和 test_sync 等实用工具的 ToolSpec 定义和 JSON 序列化。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `list_dir_tool_serializes_correctly` | 验证 list_dir 工具 JSON 序列化包含所有参数 |
| `test_sync_tool_serializes_correctly` | 验证 test_sync 工具 JSON 序列化 |
