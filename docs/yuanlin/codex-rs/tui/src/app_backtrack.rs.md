# `app_backtrack.rs` — 回溯（Backtrack）与对话记录覆盖层事件路由

## 功能概述

本文件实现了 TUI 的回溯功能，允许用户"倒回"到之前的用户消息并重新提交。回溯模式是一个小型状态机：第一次 Esc 键"预备"回溯并捕获基线线程 ID，后续 Esc 打开对话记录覆盖层并高亮某条用户消息，Enter 键请求核心进行回滚。核心确认后才修剪本地对话记录，以避免 UI 状态与 Agent 实际状态不一致。

该文件还负责对话记录覆盖层（Ctrl+T）的事件分发和渲染协调，包括活跃单元的实时尾部同步，使覆盖层能同时反映已提交的历史和正在进行的活动。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `BacktrackState` | 结构体 | 聚合所有回溯相关状态：primed 标志、base_id、选中的用户消息索引、覆盖层预览状态、待处理回滚 |
| `BacktrackSelection` | 结构体 | 用户可见的回溯选择，包含消息索引、composer 预填充文本、文本元素和图像路径 |
| `PendingBacktrackRollback` | 结构体 | 正在等待核心确认的回滚请求，携带选择和线程 ID |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle_backtrack_overlay_event` | `async fn(&mut self, tui, event) -> Result<bool>` | 路由覆盖层激活时的事件（Esc/Left 步进选择、Right 前进、Enter 确认） |
| `handle_backtrack_esc_key` | `fn(&mut self, tui)` | 处理无覆盖层时的全局 Esc 键：预备→打开预览→步进选择 |
| `apply_backtrack_rollback` | `fn(&mut self, selection)` | 发送回滚请求到核心，设置 composer 预填充（回滚确认前立即生效） |
| `open_transcript_overlay` | `fn(&mut self, tui)` | 打开对话记录覆盖层（进入备用屏幕） |
| `close_transcript_overlay` | `fn(&mut self, tui)` | 关闭对话记录覆盖层并恢复正常 UI |
| `apply_non_pending_thread_rollback` | `fn(&mut self, num_turns) -> bool` | 处理非本地发起的 ThreadRolledBack 事件 |
| `trim_transcript_cells_drop_last_n_user_turns` | `fn(cells, num_turns) -> bool` | 从对话记录末尾删除指定数量的用户轮次 |
| `user_count` | `fn(cells) -> usize` | 统计当前会话起始后的用户消息总数 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（ThreadId、TextElement）、`crossterm`（键盘事件）、`color_eyre`
- **被引用方**: `app.rs`（App 主循环中调用回溯相关方法）

## 实现备注

- 回溯使用"先请求、后修剪"模式：UI 不会在核心确认前修改对话记录，防止回滚失败时 UI 状态不一致。
- 用户消息定位通过 `TypeId` 动态类型检查实现，从最近一次 `SessionInfoCell` 开始向后扫描。
- 覆盖层的实时尾部同步利用缓存键机制，仅在活跃单元变化时刷新，避免每次绘制都重建。
- 包含完整的单元测试，验证修剪逻辑在各种边界条件下的正确性。
