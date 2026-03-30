# `connectors.rs` — ChatGPT 连接器管理模块

## 功能概述
提供面向 ChatGPT 后端的连接器（Connectors/Apps）列表获取、缓存和合并功能。通过认证令牌调用后端 API 获取目录中的所有连接器，并与 MCP 工具中可访问的连接器以及插件配置的连接器进行合并、过滤和状态标注。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `AppInfo` | 结构体（re-export） | 连接器/应用信息，包含 id、名称、logo 等 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `list_connectors` | `async fn(config) -> Result<Vec<AppInfo>>` | 获取完整连接器列表（目录+可访问+状态） |
| `list_all_connectors` | `async fn(config) -> Result<Vec<AppInfo>>` | 从后端目录获取所有连接器 |
| `list_cached_all_connectors` | `async fn(config) -> Option<Vec<AppInfo>>` | 从缓存获取连接器列表 |
| `list_all_connectors_with_options` | `async fn(config, force_refetch) -> Result<Vec<AppInfo>>` | 支持强制刷新的连接器列表获取 |
| `connectors_for_plugin_apps` | `fn(connectors, plugin_apps) -> Vec<AppInfo>` | 过滤出指定插件应用对应的连接器 |
| `merge_connectors_with_accessible` | `fn(connectors, accessible, loaded) -> Vec<AppInfo>` | 合并目录连接器与可访问连接器 |

## 依赖关系
- **引用的 crate**: `codex_core`, `codex_connectors`, `codex_login`
- **被引用方**: ChatGPT 相关上层 UI 和命令模块

## 实现备注
内部测试覆盖了 ASDK 连接器过滤规则、OpenAI 前缀过滤、不允许的连接器 ID 过滤、以及加载完成/加载中状态下的连接器合并逻辑。
