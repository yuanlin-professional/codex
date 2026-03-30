# `codex_message_processor/apps_list_helpers.rs` — Apps 列表合并、分页与通知辅助函数

## 功能概述

本模块提供 Apps（connectors）列表相关的辅助函数，包括将全量 connector 列表与可访问 connector 列表合并、判断是否需要发送 app 列表更新通知、对 connector 列表进行分页以支持游标式翻页、以及向客户端发送 `AppListUpdated` 通知。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| 无自定义结构体 | — | 仅包含辅助函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `merge_loaded_apps` | `(all_connectors, accessible_connectors) -> Vec<AppInfo>` | 合并全量与可访问的 connector 列表 |
| `should_send_app_list_updated_notification` | `(connectors, accessible_loaded, all_loaded) -> bool` | 判断是否应发送 app 列表变更通知 |
| `paginate_apps` | `(connectors, start, limit) -> Result<AppsListResponse, ...>` | 对 connector 列表进行游标分页 |
| `send_app_list_updated_notification` | `async fn(outgoing, data)` | 发送 `AppListUpdated` 服务端通知 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`（`AppInfo`、`AppsListResponse`）、`codex_chatgpt::connectors`
- **被引用方**: `codex_message_processor.rs`（处理 `apps/list` 请求时调用）

## 实现备注

- `paginate_apps` 在 cursor 超出总数时返回错误码 `INVALID_REQUEST_ERROR_CODE`。
- `merge_loaded_apps` 委托 `codex_chatgpt::connectors::merge_connectors_with_accessible` 执行实际合并逻辑。
