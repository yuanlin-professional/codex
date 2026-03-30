# `view_image.rs` -- 测试模块

## 测试覆盖范围
测试图像查看功能在 Codex 核心中的集成，包括通过 MCP 服务器返回的图像内容处理、data URL 解码、多种图像场景（纯图像、文本+图像、无效图像+有效图像等）的 TUI 渲染行为、截断策略和不同模型配置下的输入模态支持。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `view_image_tool_returns_data_url` | view_image 工具返回 data URL 格式的图像 |
| `mcp_image_content_rendered_as_image` | MCP 工具返回的图像内容被正确渲染 |
| `mcp_tool_without_image_support` | 不支持图像输入的模型回退处理 |
| `view_image_original_detail` | 请求 original 分辨率的图像查看 |
| `mcp_image_scenario_*` | 各种 MCP 图像场景的集成测试 |
