# `thread_rollout_truncation.rs` — 回滚历史的用户回合截断

## 功能概述

此文件提供基于"用户回合"边界对回滚（rollout）历史进行截断的工具函数。它通过扫描 `ResponseItem::Message` 并利用 `event_mapping::parse_turn_item` 识别用户消息边界，同时正确处理 `ThreadRolledBack` 标记以反映回滚后的有效历史。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| （无独立类型定义） | — | — |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `user_message_positions_in_rollout` | `(items: &[RolloutItem]) -> Vec<usize>` | 返回回滚历史中所有用户消息的位置索引 |
| `truncate_rollout_before_nth_user_message_from_start` | `(items, n_from_start) -> Vec<RolloutItem>` | 在第 n 个用户消息之前截断回滚历史 |

## 依赖关系

- **引用的 crate**: `crate::event_mapping`、`codex_protocol`（`TurnItem`、`ResponseItem`、`RolloutItem`、`EventMsg`）
- **被引用方**: `rollout::truncation` 模块重导出此功能

## 实现备注

- `ThreadRolledBack` 标记会从已收集的用户位置列表末尾移除对应数量的条目，模拟回滚效果
- `n_from_start = usize::MAX` 表示不截断（返回完整历史）
- 如果用户消息数量不超过 n，也返回完整历史
- 截断是严格的"之前"语义：第 n 个用户消息本身不包含在结果中
