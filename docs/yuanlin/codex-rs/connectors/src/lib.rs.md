# `lib.rs` — 连接器目录列表与缓存

## 功能概述

管理 ChatGPT 连接器（第三方应用集成）的目录列表，提供分页获取、合并去重、名称规范化、缓存管理等功能。支持从公共目录和工作区目录两个来源获取连接器信息，合并同 ID 条目的字段，生成安装 URL，并维护 1 小时 TTL 的内存缓存。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AllConnectorsCacheKey` | struct | 缓存键：base_url、account_id、user_id、is_workspace |
| `DirectoryListResponse` | struct | 目录列表 API 响应：apps 列表和分页 token |
| `DirectoryApp` | struct | 目录应用信息：id、name、description、branding、metadata 等 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `list_all_connectors_with_options` | `async (cache_key, is_workspace, force_refetch, fetch_page) -> Result<Vec<AppInfo>>` | 获取所有连接器，支持缓存和强制刷新 |
| `cached_all_connectors` | `(cache_key) -> Option<Vec<AppInfo>>` | 从内存缓存获取 |
| `merge_directory_apps` | `(apps) -> Vec<DirectoryApp>` | 按 ID 合并重复条目 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`、`serde`、`urlencoding`
- **被引用方**: app-server 的连接器管理模块

## 实现备注

- 隐藏 visibility="HIDDEN" 的应用不会出现在结果中。
- 连接器名称通过 slug 化生成安装 URL（特殊字符替换为 `-`）。
