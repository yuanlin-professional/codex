# `render.rs` -- 插件提示词渲染

## 功能概述
该文件负责将插件信息渲染为发送给语言模型的提示词文本。提供两个渲染函数：一个生成包含所有启用插件列表和使用指南的全局插件章节，另一个为被显式提及的单个插件生成具体的能力说明。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `render_plugins_section` | `fn(plugins: &[PluginCapabilitySummary]) -> Option<String>` | 渲染全局插件章节，包含可用插件列表和使用指南。空列表时返回 None。内容被 `<plugins_instructions>` 标签包裹 |
| `render_explicit_plugin_instructions` | `fn(plugin, mcp_servers, apps) -> Option<String>` | 为单个插件渲染能力说明，列出其技能前缀、MCP 服务器和应用。无有效能力时返回 None |

## 依赖关系
- **引用的 crate**: `codex_protocol::protocol`（标签常量）
- **引用的内部模块**: `crate::plugins::PluginCapabilitySummary`
- **被引用方**: `mod.rs` 通过 `pub(crate) use` 导出；`injection.rs` 调用 `render_explicit_plugin_instructions`

## 实现备注
- 全局插件章节包含标准化的使用指南，涵盖发现机制、技能命名、触发规则、能力关系、偏好规则和降级行为
- 单个插件说明仅在至少有一项能力（技能/MCP 服务器/应用）时才生成
- 插件描述直接使用 `display_name` 字段
