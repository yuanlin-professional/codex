# `injection.rs` -- 插件注入构建器

## 功能概述
该文件负责为被显式提及（mentioned）的插件生成注入到对话中的开发者提示（developer hints）。当用户在消息中提及特定插件时，系统会将该插件关联的 MCP 服务器、应用连接器和技能前缀信息打包为 `ResponseItem`，引导模型使用该插件的能力。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_plugin_injections` | `fn(mentioned_plugins: &[PluginCapabilitySummary], mcp_tools: &HashMap<String, ToolInfo>, available_connectors: &[AppInfo]) -> Vec<ResponseItem>` | 为每个被提及的插件构建注入项。过滤出与插件关联的 MCP 服务器和已启用的应用连接器，渲染为开发者指令后包装为 ResponseItem |

## 依赖关系
- **引用的 crate**: `codex_protocol`
- **引用的内部模块**: `crate::connectors`, `crate::mcp::CODEX_APPS_MCP_SERVER_NAME`, `crate::mcp_connection_manager::ToolInfo`, `super::PluginCapabilitySummary`, `super::render_explicit_plugin_instructions`
- **被引用方**: `mod.rs` 通过 `pub(crate) use` 导出

## 实现备注
- 排除 `CODEX_APPS_MCP_SERVER_NAME` 对应的内置 MCP 服务器
- 使用 `BTreeSet` 对 MCP 服务器名和应用标签进行去重和排序
- 通过插件的 `display_name` 匹配工具和连接器的归属关系
