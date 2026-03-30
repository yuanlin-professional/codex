# `spec_tests.rs` — 测试模块

## 测试覆盖范围
验证工具规格构建的各种场景，包括 shell 类型选择、MCP 工具转换、apply_patch 工具类型、Code Mode 工具列表、feature flag 组合以及工具搜索描述生成等。该测试文件内容丰富，覆盖了 `spec.rs` 中几乎所有配置路径。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| MCP 工具转换测试 | 验证 MCP 工具到 ResponsesApiTool 的正确转换 |
| shell 类型选择测试 | 验证不同 feature flag 组合下的 shell 工具选择逻辑 |
| apply_patch 类型测试 | 验证 Freeform 和 Function 两种补丁工具类型 |
| Code Mode 嵌套工具测试 | 验证 Code Mode 开启时的嵌套工具列表构建 |
| tool_search 描述测试 | 验证工具搜索描述模板的动态渲染 |
| tool_suggest 配置测试 | 验证可发现工具建议功能的规格构建 |
| web_search 配置测试 | 验证不同 web_search_mode 下的工具配置 |
| multi_agent v1/v2 测试 | 验证多代理工具的版本选择和配置 |
| unified_exec 环境限制测试 | 验证 Windows 沙箱环境下的 unified_exec 可用性 |
| dynamic_tools 测试 | 验证动态工具的注册和规格构建 |
