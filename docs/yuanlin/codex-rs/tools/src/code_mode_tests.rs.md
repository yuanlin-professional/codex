# `code_mode_tests.rs` -- 测试模块

## 测试覆盖范围
验证 code mode 相关的工具定义增强和转换逻辑，包括工具描述的代码示例注入和 ToolSpec 到 CodeModeToolDefinition 的转换。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `augment_tool_spec_adds_code_mode_description` | 验证 Function 工具描述被增强为包含代码模式示例 |
| `augment_tool_spec_adds_freeform_description` | 验证 Freeform 工具描述增强 |
| `tool_spec_to_code_mode_tool_definition_for_shell` | shell 工具转换为 code mode 定义 |
| `create_code_mode_tool_spec` | 验证 code_mode 控制工具创建 |
| `create_wait_tool_spec` | 验证 wait 工具创建 |
