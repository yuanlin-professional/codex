# `agent_navigation.rs` — 多代理 picker 导航与标签状态管理

## 功能概述

本模块维护多代理（multi-agent）picker 的稳定生成顺序缓存，用于 `/agent` picker 的键盘导航（上/下一个）和当前活动线程的 footer 标签显示。它将纯粹的导航逻辑从 `App` 中分离出来，保持可独立测试。核心不变量是：遍历顺序遵循首次发现（first-seen）的生成顺序，而非 thread-id 排序。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AgentNavigationState` | struct | 多代理 picker 排序和标签的状态容器，包含 `threads` 映射和 `order` 向量 |
| `AgentNavigationDirection` | enum | 键盘遍历方向：`Previous`（向前）和 `Next`（向后） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `upsert` | `(&mut self, thread_id, nickname, role, is_closed)` | 插入或更新 picker 条目，保持首次发现顺序不变 |
| `mark_closed` | `(&mut self, thread_id)` | 标记线程为已关闭但保留在遍历缓存中 |
| `adjacent_thread_id` | `(&self, current, direction) -> Option<ThreadId>` | 返回键盘导航中相邻的线程 ID（支持环绕） |
| `active_agent_label` | `(&self, current, primary) -> Option<String>` | 为当前显示的线程生成 footer 标签 |
| `picker_subtitle` | `() -> String` | 从快捷键绑定生成 `/agent` picker 副标题 |
| `ordered_threads` | `(&self) -> Vec<(ThreadId, &Entry)>` | 按生成顺序返回所有 picker 行 |

## 依赖关系

- **引用的 crate**: `codex_protocol::ThreadId`, `ratatui::text::Span`
- **被引用方**: `App` 使用此模块管理多代理导航状态

## 实现备注

- `order` 向量记录首次出现的 thread id，保证遍历顺序稳定
- 关闭的线程保留在 picker 中，避免导航形状突变
- 包含完整的单元测试验证遍历环绕、顺序保持和标签生成
