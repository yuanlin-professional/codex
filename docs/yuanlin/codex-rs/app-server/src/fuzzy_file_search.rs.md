# `fuzzy_file_search.rs` — 模糊文件搜索（一次性与会话式）

## 功能概述

本模块提供两种模糊文件搜索模式。一次性搜索通过 `run_fuzzy_file_search` 执行阻塞搜索并返回结果列表。会话式搜索通过 `start_fuzzy_file_search_session` 创建持久搜索会话，支持实时查询更新和增量结果推送（通过 `FuzzyFileSearchSessionUpdated` 和 `FuzzyFileSearchSessionCompleted` 通知）。两种模式均利用 `codex_file_search` crate 在后台线程中执行搜索，限制最多 50 个匹配结果和 12 个搜索线程。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `FuzzyFileSearchSession` | 结构体 | 会话式搜索句柄，支持 `update_query` 更新查询 |
| `SessionShared` | 结构体 | 会话共享状态（session_id、最新查询、outgoing sender 等） |
| `SessionReporterImpl` | 结构体 | 实现 `file_search::SessionReporter` trait，将搜索快照转换为通知 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_fuzzy_file_search` | `async fn(query, roots, cancellation_flag) -> Vec<FuzzyFileSearchResult>` | 一次性模糊搜索 |
| `start_fuzzy_file_search_session` | `fn(session_id, roots, outgoing) -> Result<FuzzyFileSearchSession>` | 创建会话式搜索 |
| `FuzzyFileSearchSession::update_query` | `fn(&self, query)` | 更新搜索查询（会话模式） |

## 依赖关系

- **引用的 crate**: `codex_file_search`（搜索引擎）、`codex_app_server_protocol`（`FuzzyFileSearchResult` 等通知类型）
- **被引用方**: `codex_message_processor.rs`（处理 `fuzzyFileSearch` 和 `fuzzyFileSearch/session/*` 请求）

## 实现备注

- 会话在 `Drop` 时通过 `AtomicBool` 标志取消搜索。
- 查询为空字符串时不返回结果。
- 结果按分数降序、路径升序排列。
