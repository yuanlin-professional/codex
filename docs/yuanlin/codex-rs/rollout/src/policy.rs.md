# `policy.rs` — Rollout 事件持久化策略

## 功能概述
该文件定义了 Rollout 事件的持久化策略，决定哪些 `RolloutItem` 应被写入 rollout 文件。`EventPersistenceMode` 枚举区分 Limited 和 Extended 两种模式。提供 `is_persisted_response_item`、`should_persist_response_item` 和 `should_persist_response_item_for_memories` 三个过滤函数。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `EventPersistenceMode` | 枚举 | 事件持久化模式：Limited/Extended |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `is_persisted_response_item` | `(item, mode) -> bool` | 判断条目是否应被持久化 |
| `should_persist_response_item` | `(item) -> bool` | 判断 ResponseItem 是否应被持久化 |
| `should_persist_response_item_for_memories` | `(item) -> bool` | 判断 ResponseItem 是否应被持久化用于记忆 |

## 依赖关系
- **引用的 crate**: `codex_protocol`
- **被引用方**: `recorder.rs`

## 实现备注
- ResponseItem::Other 永远不持久化
- SessionMeta/Compacted/TurnContext 始终持久化
- 记忆持久化过滤会排除 developer 角色的消息
