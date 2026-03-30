# `mentions.rs` -- 插件和工具提及解析

## 功能概述
该文件负责从用户输入消息中提取工具和插件的提及（mentions）。支持两种提及方式：结构化的 `UserInput::Mention` 和文本中的链接语法（如 `[$name](app://id)` 和 `[@name](plugin://id)`）。提取结果用于确定需要激活的应用连接器和插件注入。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `CollectedToolMentions` | struct | 收集到的工具提及，包含普通名称集合和路径集合 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `collect_tool_mentions_from_messages` | `fn(messages: &[String]) -> CollectedToolMentions` | 从消息文本中提取 `$` 符号标记的工具提及 |
| `collect_explicit_app_ids` | `fn(input: &[UserInput]) -> HashSet<String>` | 从用户输入中收集显式的应用连接器 ID（`app://` 协议） |
| `collect_explicit_plugin_mentions` | `fn(input: &[UserInput], plugins: &[PluginCapabilitySummary]) -> Vec<PluginCapabilitySummary>` | 收集显式的插件提及（`plugin://` 协议），使用 `@` 符号标记 |
| `build_connector_slug_counts` | `fn(connectors: &[AppInfo]) -> HashMap<String, usize>` | 统计连接器的 slug 计数 |
| `build_skill_name_counts` | 宏重导出 | 构建技能名称计数映射 |

## 依赖关系
- **引用的 crate**: `codex_protocol`
- **引用的内部模块**: `crate::connectors`, `crate::injection`（工具提及提取逻辑）, `crate::mention_syntax`（符号常量）, `super::PluginCapabilitySummary`
- **被引用方**: `mod.rs` 通过 `pub(crate) use` 导出五个函数

## 实现备注
- 工具提及使用 `$` 符号（`TOOL_MENTION_SIGIL`），插件提及使用 `@` 符号（`PLUGIN_TEXT_MENTION_SIGIL`）
- 两种链接语法不混用：`[$name](plugin://...)` 会被忽略（因为使用了 `$` 而非 `@`）
- 同时支持结构化提及（`UserInput::Mention`）和链接文本提及，结果自动去重
