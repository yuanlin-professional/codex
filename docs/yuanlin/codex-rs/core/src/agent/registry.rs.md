# `registry.rs` — Agent 注册表与线程限制管理

## 功能概述

`AgentRegistry` 是多 agent 系统的注册表，负责管理活跃 agent 的元数据、线程数限制、昵称分配和路径索引。它通过原子计数器和互斥锁实现线程安全的并发访问，在同一用户 session 中的所有 agent 之间共享（因为 `AgentControl` 是共享的）。该模块还提供了 `SpawnReservation` RAII 守卫，确保 spawn 失败时自动释放预留的资源。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AgentRegistry` | struct | 注册表核心结构，包含活跃 agent 映射和总计数器 |
| `ActiveAgents` | struct (私有) | 内部状态，含 agent 树（路径到元数据映射）、已用昵称集合、昵称重置计数 |
| `AgentMetadata` | struct | agent 元数据，含 `agent_id`、`agent_path`、`agent_nickname`、`agent_role`、`last_task_message` |
| `SpawnReservation` | struct | RAII 守卫，在 spawn 流程中预留资源，drop 时自动回收未提交的预留 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `reserve_spawn_slot` | `fn(&Arc<Self>, max_threads) -> Result<SpawnReservation>` | 预留一个 spawn 槽位，超限返回 `AgentLimitReached` |
| `release_spawned_thread` | `fn(&self, thread_id)` | 释放指定线程占用的槽位和注册信息 |
| `register_root_thread` | `fn(&self, thread_id)` | 注册根线程到 agent 树 |
| `agent_id_for_path` | `fn(&self, path) -> Option<ThreadId>` | 按路径查找 agent 的 ThreadId |
| `agent_metadata_for_thread` | `fn(&self, thread_id) -> Option<AgentMetadata>` | 按 ThreadId 查找 agent 元数据 |
| `live_agents` | `fn(&self) -> Vec<AgentMetadata>` | 获取所有活跃（非根）agent 的元数据列表 |
| `update_last_task_message` | `fn(&self, thread_id, message)` | 更新指定 agent 的最后任务消息 |
| `reserve_agent_nickname_with_preference` | `fn(&mut SpawnReservation, names, preferred) -> Result<String>` | 预留昵称，支持优先使用指定昵称 |
| `reserve_agent_path` | `fn(&mut SpawnReservation, path) -> Result<()>` | 预留 agent 路径，防止重复 |
| `commit` | `fn(SpawnReservation, metadata)` | 提交预留，正式注册 agent |
| `next_thread_spawn_depth` | `fn(source) -> i32` | 计算下一个 spawn 深度 |
| `exceeds_thread_spawn_depth_limit` | `fn(depth, max_depth) -> bool` | 检查是否超过最大 spawn 深度限制 |
| `format_agent_nickname` | `fn(name, reset_count) -> String` | 格式化昵称，池耗尽后添加序号后缀（如 "the 2nd"） |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`AgentPath`、`ThreadId`、`SessionSource`）、`codex_otel`（metrics）、`rand`
- **引用的内部模块**: `crate::error`
- **被引用方**: `agent::control::AgentControl`

## 实现备注

- **CAS 原子操作**: `try_increment_spawned` 使用 `compare_exchange_weak` 循环实现无锁的线程数限制检查和递增。
- **RAII 资源管理**: `SpawnReservation` 的 `Drop` 实现确保即使 spawn 过程中发生 panic 或错误，预留的计数和路径也会被正确释放。
- **昵称池重置**: 当所有候选昵称都已使用后，清空已用集合并递增重置计数器，之后的昵称会附加序号后缀（如 "Plato the 2nd"、"Plato the 3rd"）。
- **英语序数词后缀**: `format_agent_nickname` 正确处理了 11th/12th/13th 等特殊情况。
