# `list.rs` — Rollout 文件发现与线程列表

## 功能概述
该文件提供 rollout 文件的发现和线程列表功能。支持按日期目录扫描 rollout 文件、从文件名解析时间戳和 UUID、提取线程摘要信息、分页游标管理以及按 ID 查找特定线程的 rollout 路径。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ThreadsPage` | 结构体 | 线程列表分页结果 |
| `ThreadItem` | 结构体 | 单个线程摘要 |
| `Cursor` | 类型 | 分页游标 |
| `ThreadSortKey` | 枚举 | 排序方式 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `get_threads` | 函数 | 获取线程列表 |
| `find_thread_path_by_id_str` | 函数 | 按 ID 查找 rollout 路径 |

## 依赖关系
- **引用的 crate**: `codex_protocol`, `codex_file_search`, `time`, `uuid`
- **被引用方**: `recorder.rs`, `state_db.rs`

## 实现备注
- 文件名格式：`rollout-{timestamp}_{uuid}.jsonl`
