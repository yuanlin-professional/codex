# `tool_search_tests.rs` — 测试模块

## 测试覆盖范围
测试覆盖了 `tool_search` 工具的结果序列化逻辑，验证搜索结果按命名空间正确分组，以及在缺少 `connector_description` 时使用 `connector_name` 作为回退描述的行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `serialize_tool_search_output_tools_groups_results_by_namespace` | 验证来自不同命名空间（calendar、gmail）的工具被正确分组，每个命名空间包含对应的工具列表且使用 `connector_description` 作为描述 |
| `serialize_tool_search_output_tools_falls_back_to_connector_name_description` | 验证当 `connector_description` 为 None 时，使用 "Tools for working with {connector_name}." 格式生成描述 |
