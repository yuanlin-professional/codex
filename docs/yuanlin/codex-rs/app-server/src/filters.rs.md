# `filters.rs` — 线程来源过滤器计算与匹配

## 功能概述

本模块提供线程列表的来源过滤功能。`compute_source_filters` 根据客户端请求的 `ThreadSourceKind` 列表计算两级过滤策略：对于仅包含交互式来源（Cli、VsCode）的过滤器，可以直接在 `ThreadManager` 查询时过滤；对于包含非交互式来源（Exec、AppServer、SubAgent 各变体等）的过滤器，需要获取全量结果后再进行后过滤。`source_kind_matches` 用于后过滤阶段逐项匹配。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| 无自定义结构体 | — | 仅包含函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `compute_source_filters` | `fn(source_kinds) -> (Vec<CoreSessionSource>, Option<Vec<ThreadSourceKind>>)` | 计算预过滤和后过滤参数 |
| `source_kind_matches` | `fn(source, filter) -> bool` | 判断单个 session source 是否匹配过滤条件 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`（`ThreadSourceKind`）、`codex_core`（`INTERACTIVE_SESSION_SOURCES`）、`codex_protocol`（`SessionSource`、`SubAgentSource`）
- **被引用方**: `codex_message_processor.rs`（处理 `thread/list` 请求时调用）

## 实现备注

- SubAgent 来源支持细粒度过滤：`SubAgentReview`、`SubAgentCompact`、`SubAgentThreadSpawn`、`SubAgentOther`。
- 空的 `source_kinds` 列表等同于未指定过滤器，默认返回交互式来源。
- 包含单元测试验证默认行为、纯交互式过滤、SubAgent 变体区分等场景。
