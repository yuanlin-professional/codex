# `codex_message_processor/plugin_app_helpers.rs` — 插件关联 App 的元数据加载与授权状态检测

## 功能概述

本模块提供与插件关联的 App connector 的辅助功能。`load_plugin_app_summaries` 根据插件声明的 `AppConnectorId` 列表，异步加载对应 connector 的元数据并检测其授权状态（是否需要用户登录），返回 `AppSummary` 列表。`plugin_apps_needing_auth` 则在已加载的全量和可访问 connector 列表中筛选出需要授权的插件关联 App。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| 无自定义结构体 | — | 仅包含辅助函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_plugin_app_summaries` | `async fn(config, plugin_apps) -> Vec<AppSummary>` | 加载插件所需 App 的摘要信息（含授权状态） |
| `plugin_apps_needing_auth` | `fn(all, accessible, plugin_apps, codex_apps_ready) -> Vec<AppSummary>` | 返回需要用户授权的插件关联 App 列表 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`（`AppInfo`、`AppSummary`）、`codex_chatgpt::connectors`、`codex_core::plugins`
- **被引用方**: `codex_message_processor.rs`（处理 `plugin/read` 请求时调用）

## 实现备注

- 当 `codex_apps_ready` 为 `false` 时，`plugin_apps_needing_auth` 直接返回空列表，因为无法确定授权状态。
- `load_plugin_app_summaries` 在加载失败时会回退到缓存的 connector 列表。
- 包含针对 `plugin_apps_needing_auth` 的单元测试。
