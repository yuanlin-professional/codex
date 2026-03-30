# `view_image_tests.rs` -- 测试模块

## 测试覆盖范围
验证 view_image 工具定义的参数构建和 output_schema。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `view_image_tool_includes_detail_param` | 启用 original detail 选项时包含 detail 参数 |
| `view_image_tool_excludes_detail_param` | 禁用 original detail 选项时不包含 detail 参数 |
| `view_image_tool_output_schema` | 验证 output_schema 包含 image_url 和 detail 字段 |
