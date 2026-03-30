# `discoverable.rs` -- 可发现插件列表生成

## 功能概述
该文件负责从 OpenAI 精选市场（curated marketplace）中筛选出可被工具建议（tool suggest）发现的插件列表。它根据配置的白名单和已安装状态过滤插件，并返回排序后的插件能力摘要列表。核心功能是为工具建议系统提供尚未安装的可发现插件信息。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `TOOL_SUGGEST_DISCOVERABLE_PLUGIN_ALLOWLIST` | 常量数组 | 内置的可发现插件白名单，包含 github、notion、slack 等 8 个精选插件 ID |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `list_tool_suggest_discoverable_plugins` | `fn(config: &Config) -> Result<Vec<PluginCapabilitySummary>>` | 列出未安装的可发现插件。先检查插件功能是否启用，再从精选市场中筛选白名单内或配置中指定的未安装插件，按显示名称排序后返回 |

## 依赖关系
- **引用的 crate**: `anyhow`, `tracing`, `codex_features`
- **引用的内部模块**: `super::PluginsManager`, `super::PluginCapabilitySummary`, `super::PluginReadRequest`, `crate::config::Config`, `crate::config::types::ToolSuggestDiscoverableType`
- **被引用方**: `mod.rs` 通过 `pub(crate) use` 导出给 crate 内部使用

## 实现备注
- 仅在插件功能（`Feature::Plugins`）启用时才会返回结果，否则返回空列表
- 白名单机制与配置中的 `tool_suggest.discoverables` 并行生效：白名单内的插件或配置中显式指定的插件 ID 均可被发现
- 已安装的插件会被自动排除
- 结果按 `display_name` 排序，相同时按 `config_name` 二次排序
