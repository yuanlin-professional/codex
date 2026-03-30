# `file_search.rs` — `@` 文件搜索会话管理

## 功能概述

本文件实现了基于会话的 `@` 文件搜索编排。当用户在 composer 中输入 `@token` 时，`FileSearchManager` 创建或更新一个 `codex-file-search` 搜索会话，在每次按键时更新查询，并在搜索结果就绪时通过 `AppEvent::FileSearchResult` 将结果推送到 App 层。查询清空时会话自动销毁。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `FileSearchManager` | 结构体 | 管理文件搜索状态、搜索目录和 App 事件发送器 |
| `SearchState` | 结构体 | 内部状态：最新查询、当前搜索会话、会话令牌 |
| `TuiSessionReporter` | 结构体 | 实现 `SessionReporter` trait，将搜索快照推送为 AppEvent |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(search_dir, tx) -> Self` | 创建搜索管理器 |
| `update_search_dir` | `fn(&mut self, new_dir)` | 更新搜索目录（会话恢复时 CWD 变更） |
| `on_user_query` | `fn(&self, query)` | 处理用户查询更新：创建/更新/销毁搜索会话 |

## 依赖关系

- **引用的 crate**: `codex_file_search`（文件搜索引擎）
- **被引用方**: `app.rs`（App 持有 FileSearchManager 并在 StartFileSearch 事件时调用）

## 实现备注

使用 `Arc<Mutex<SearchState>>` 实现线程安全的状态共享。会话令牌机制确保已过时会话的回调结果不会被误发送。搜索选项启用了 `compute_indices` 以支持匹配位置高亮。
