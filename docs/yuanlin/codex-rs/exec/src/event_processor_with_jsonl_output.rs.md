# `event_processor_with_jsonl_output.rs` — JSONL 格式事件输出处理器

## 功能概述

实现了 `EventProcessor` trait 的 JSONL 输出版本，用于 `--json` 模式。将 app-server 通知转换为结构化的 `ThreadEvent` 枚举，每行输出一个 JSON 事件到 stdout。支持 item 的 started/updated/completed 生命周期管理、todo 列表跟踪、ID 映射（raw → exec item id）以及 turn 完成时的未完成项回收。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `EventProcessorWithJsonOutput` | struct | JSONL 处理器主体，持有 ID 映射、todo 列表、token 用量等状态 |
| `RunningTodoList` | struct (内部) | 跟踪当前活跃的 todo 列表 item_id 和 items |
| `CollectedThreadEvents` | struct | 批量收集的事件及对应的 `CodexStatus` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `(last_message_path) -> Self` | 构造新实例 |
| `collect_thread_events` | `(&mut self, notification) -> CollectedThreadEvents` | 将 ServerNotification 转换为 ThreadEvent 列表 |
| `collect_warning` | `(&mut self, message) -> CollectedThreadEvents` | 将警告转为 Error item 事件 |
| `map_item_with_id` | `(item, make_id) -> Option<ExecThreadItem>` | 将 app-server ThreadItem 映射为 exec 格式的 ThreadItem |
| `map_todo_items` | `(plan) -> Vec<TodoItem>` | 将 TurnPlanStep 列表映射为 TodoItem |
| `reconcile_unfinished_started_items` | `(&mut self, turn_items) -> Vec<ThreadEvent>` | 在 turn 完成时回收之前 started 但未 completed 的 items |
| `thread_started_event` | `(session_configured) -> ThreadEvent` | 生成线程启动事件 |
| `emit` | `(&self, event)` | 序列化并输出事件到 stdout |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`、`codex_core`、`codex_protocol`、`serde_json`
- **被引用方**: `exec/src/lib.rs`（`--json` 模式下使用）

## 实现备注

- item ID 通过 `raw_to_exec_item_id` HashMap 进行从 app-server ID 到 exec 自增 ID 的映射。
- 当 turn 失败时，`final_message` 被清除以避免将上一轮的 output-last-message 文件覆盖。
- TurnPlanUpdated 事件产生 TodoList item 的 started/updated 生命周期。
