# `start.rs` -- 记忆启动管线入口

## 功能概述

提供记忆启动管线的单一入口函数 `start_memories_startup_task`。该函数检查前置条件（非临时会话、功能开关已启用、非子代理会话、状态数据库可用），然后在后台 tokio 任务中依次执行 Phase 1 修剪、Phase 1 提取和 Phase 2 合并。使用弱引用 (`Arc::downgrade`) 避免延长会话生命周期。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_memories_startup_task` | `fn(&Arc<Session>, Arc<Config>, &SessionSource)` | 记忆启动管线入口，检查条件后在后台执行三步管线 |

## 依赖关系

- **引用的 crate**: `codex_features`、`codex_protocol`
- **被引用方**: `memories/mod.rs` 通过 `pub(crate) use` 导出，供 `codex` 主会话初始化时调用

## 实现备注

- 管线按顺序执行：修剪旧记忆 -> Phase 1 提取 -> Phase 2 合并，确保数据一致性。
- 使用 `Arc::downgrade` 创建弱引用，若会话在管线运行前被释放，任务会安静退出。
- 对子代理会话（`SessionSource::SubAgent`）跳过记忆管线，避免递归生成记忆。
