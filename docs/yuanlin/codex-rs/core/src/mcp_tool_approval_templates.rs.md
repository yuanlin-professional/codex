# `mcp_tool_approval_templates.rs` — MCP 工具审批提示模板渲染

## 功能概述

该文件实现了 MCP（Model Context Protocol）工具调用的用户审批提示模板系统。当 MCP 工具被标记为"有后果的"（consequential）时，需要在执行前向用户展示友好的审批提示。模板从内嵌的 JSON 文件（`consequential_tool_message_templates.json`）中加载，根据 connector ID、server name 和 tool title 精确匹配模板，然后渲染模板中的变量（如 `{connector_name}`）和工具参数的可读标签。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RenderedMcpToolApprovalTemplate` | struct | 渲染后的审批模板：问题文本、elicitation 消息、工具参数及其展示列表 |
| `RenderedMcpToolApprovalParam` | struct | 渲染后的参数展示项：参数名、值、友好显示名称 |
| `ConsequentialToolMessageTemplate` | struct (内部) | 模板定义：connector_id、server_name、tool_title、模板字符串、参数映射 |
| `ConsequentialToolTemplateParam` | struct (内部) | 模板参数映射：参数名到友好标签的映射 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `render_mcp_tool_approval_template` | `fn(server_name, connector_id, connector_name, tool_title, tool_params) -> Option<Rendered>` | 公开入口：从全局模板列表中查找并渲染 |
| `render_mcp_tool_approval_template_from_templates` | `fn(templates, ...) -> Option<Rendered>` | 核心渲染逻辑：精确匹配模板 + 渲染问题和参数 |
| `render_question_template` | `fn(template, connector_name) -> Option<String>` | 渲染问题模板，替换 `{connector_name}` 变量 |
| `render_tool_params` | `fn(tool_params, template_params) -> Option<(Option<Value>, Vec<RenderedParam>)>` | 渲染工具参数：模板定义的参数使用友好标签，其余按字母序排列 |
| `load_consequential_tool_message_templates` | `fn() -> Option<Vec<Template>>` | 通过 `LazyLock` 延迟加载内嵌 JSON 模板文件 |

## 依赖关系

- **引用的 crate**: `serde`/`serde_json`（JSON 解析和序列化）、`tracing`
- **被引用方**: `crate::mcp_tool_call`（MCP 工具调用审批流程）

## 实现备注

- 模板通过 `include_str!` 在编译时嵌入 JSON 文件，运行时通过 `LazyLock` 延迟解析一次。
- 模板文件有 schema 版本检查（当前版本 4），版本不匹配时返回 `None` 而非崩溃。
- 参数渲染时会检测标签冲突（同一标签被多个参数使用），冲突时返回 `None` 拒绝渲染。
- 模板参数优先按模板定义顺序排列，未被模板覆盖的参数按名称字母序追加。
- `{connector_name}` 变量替换是可选的：如果模板不含该变量则直接使用字面文本；如果含有但 connector_name 为空或 None 则返回 `None`。
