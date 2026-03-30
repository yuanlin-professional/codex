# `threads.rs` — 线程元数据 SQLite 持久化操作

## 功能概述
该文件为 `StateRuntime` 实现线程（Thread）元数据的完整 CRUD 和查询操作。包括单线程查询/更新、分页列表、归档/取消归档、Rollout 条目增量应用、动态工具持久化、线程生成边（parent-child spawn edge）管理等功能。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无新增类型 | — | 类型定义在 `model/thread_metadata.rs` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `get_thread` | `async (&self, id) -> Result<Option<ThreadMetadata>>` | 按 ID 查询线程元数据 |
| `list_threads` | `async (&self, page_size, ...) -> Result<ThreadsPage>` | 分页列表线程（支持排序、来源/提供者过滤、搜索） |
| `upsert_thread` | `async (&self, metadata) -> Result<()>` | 插入或更新线程元数据 |
| `apply_rollout_items` | `async (&self, builder, items, ...) -> Result<()>` | 增量应用 rollout 条目到线程元数据 |
| `upsert_thread_spawn_edge` | `async (&self, parent, child, status) -> Result<()>` | 创建/更新线程生成边 |
| `list_thread_spawn_descendants_with_status` | `async (&self, root, status) -> Result<Vec<ThreadId>>` | 递归列出特定状态的后代线程 |
| `persist_dynamic_tools` | `async (&self, thread_id, tools) -> Result<()>` | 持久化线程的动态工具定义 |

## 依赖关系
- **引用的 crate**: `sqlx`, `codex_protocol`
- **被引用方**: `codex-rollout`, `codex-core`

## 实现备注
- 线程生成边使用递归 CTE 实现后代查询
- `apply_rollout_items` 保留现有的 Git 信息不被新数据覆盖
- memory_mode 在创建时可指定初始值，后续更新不改变
