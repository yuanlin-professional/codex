# `citations.rs` -- 记忆引用解析与线程 ID 提取

## 功能概述

该文件负责解析模型输出中的记忆引用（memory citation）标记。它从包含 XML 风格标签（`<citation_entries>`、`<rollout_ids>`、`<thread_ids>`）的文本中提取结构化引用条目和 rollout/thread ID 列表，并将其转换为 `MemoryCitation` 和 `ThreadId` 类型。支持新旧两种 ID 标签格式（`<thread_ids>` 和遗留的 `<rollout_ids>`），同时对 rollout ID 做去重处理。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `MemoryCitation` | 外部结构体 (codex_protocol) | 包含引用条目列表和 rollout ID 列表的聚合结果 |
| `MemoryCitationEntry` | 外部结构体 (codex_protocol) | 单条引用条目，包括文件路径、行范围和注释 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_memory_citation` | `fn(Vec<String>) -> Option<MemoryCitation>` | 从引用文本列表中解析出 `MemoryCitation`，提取条目和去重后的 rollout ID |
| `get_thread_id_from_citations` | `fn(Vec<String>) -> Vec<ThreadId>` | 从引用文本中提取有效的 `ThreadId` 列表 |
| `parse_memory_citation_entry` | `fn(&str) -> Option<MemoryCitationEntry>` | 解析单行引用条目，格式为 `path:start-end\|note=[...]` |
| `extract_block` | `fn(&str, &str, &str) -> Option<&str>` | 通用 XML 标签块提取工具函数 |
| `extract_ids_block` | `fn(&str) -> Option<&str>` | 提取 `<rollout_ids>` 或 `<thread_ids>` 块，兼容新旧格式 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`ThreadId`、`MemoryCitation`、`MemoryCitationEntry`）、`std::collections::HashSet`
- **被引用方**: 记忆子系统中需要从模型响应中提取引用信息的模块

## 实现备注

- 引用条目的格式为 `path:line_start-line_end|note=[note_text]`，使用 `rsplit_once` 从右向左解析以处理路径中可能包含 `|` 或 `:` 的情况。
- rollout ID 使用 `HashSet` 去重，但保留首次出现的顺序。
- `extract_ids_block` 优先尝试 `<rollout_ids>` 标签，失败后回退到 `<thread_ids>` 标签，实现了向后兼容。
