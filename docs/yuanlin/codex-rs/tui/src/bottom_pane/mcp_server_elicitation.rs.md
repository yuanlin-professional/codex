# `mcp_server_elicitation.rs` -- MCP 服务器 Elicitation 表单叠加层

## 功能概述
该文件实现了 MCP 服务器 elicitation 表单叠加层（McpServerElicitationOverlay），用于处理来自 MCP 服务器的用户信息收集请求。支持多种表单字段类型：文本输入、单选按钮、多行文本和布尔确认。包含工具建议（tool suggestion）功能，可以建议用户安装或启用应用。表单支持多字段切换和 JSON schema 驱动的表单生成。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `McpServerElicitationFormRequest` | 结构体 | 封装 MCP elicitation 请求，包含线程 ID、服务器名、请求参数等 |
| `McpServerElicitationOverlay` | 结构体 | 表单叠加层主结构，管理表单字段、编辑器和导航状态 |
| `ToolSuggestionType` | 枚举 | 工具建议类型：Install 或 Enable |
| `ToolSuggestion` | 结构体 | 工具建议数据，包含工具 ID、名称、安装 URL、建议原因等 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(request, app_event_tx, ...) -> Self` | 从请求创建表单叠加层 |
| `submit` | `fn submit(&mut self)` | 提交表单数据，发送 elicitation 决策 |
| `handle_key_event` | `fn handle_key_event(&mut self, key_event)` | 处理键盘导航和输入 |
| `tool_suggestion` | `fn tool_suggestion(&self) -> Option<&ToolSuggestion>` | 提取工具建议信息 |

## 依赖关系
- **引用的 crate**: `codex_app_server_protocol`、`codex_protocol`（ElicitationAction）、`serde_json`、`crossterm`、`ratatui`
- **被引用方**: `mod.rs` 导出 `McpServerElicitationFormRequest` 和 `McpServerElicitationOverlay`

## 实现备注
- 表单字段从 JSON schema 解析生成，支持 enum 单选和 primitive 类型
- 使用内嵌的 ChatComposer 处理文本输入字段
- 支持 Tab/Shift+Tab 在字段间切换焦点
- 工具建议模式下会路由到 AppLinkView 而非表单
