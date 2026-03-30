# `multi_agents.rs` — 多代理状态渲染和导航

## 功能概述

负责多代理（multi-agent/collaboration）模式下的 TUI 展示层。包括代理选择器条目的格式化、
协作事件（spawn、interaction、waiting、close、resume）的历史记录单元格渲染、
以及代理间快速切换的键盘快捷键匹配。高层协调逻辑（活跃线程切换、关闭决策）
留在 `App` 中处理。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AgentPickerThreadEntry` | struct | 代理选择器中的线程条目 |
| `SpawnRequestSummary` | struct | spawn 请求的模型和推理参数摘要 |
| `AgentLabel` | struct | 代理显示标签（线程 ID、昵称、角色） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `spawn_end` | `(event, request) -> PlainHistoryCell` | 渲染 spawn 完成事件 |
| `interaction_end` | `(event) -> PlainHistoryCell` | 渲染交互完成事件 |
| `waiting_begin/end` | `(event) -> PlainHistoryCell` | 渲染等待开始/结束事件 |
| `close_end` / `resume_begin/end` | `(event) -> PlainHistoryCell` | 渲染关闭/恢复事件 |
| `previous/next_agent_shortcut_matches` | `(key, fallback) -> bool` | 匹配代理切换快捷键 |
| `format_agent_picker_item_name` | `(nickname, role, is_primary) -> String` | 格式化代理名称 |

## 依赖关系

- **引用的 crate**: `codex_protocol`, `crossterm`, `ratatui`
- **引用的模块**: `crate::history_cell`, `crate::render::line_utils`, `crate::text_formatting`, `crate::key_hint`

## 实现备注

- macOS 上额外支持 Option+B/F 作为 Alt+Left/Right 的回退快捷键（当 enhanced key reporting 不可用时）。
- 仅在 composer 为空时启用 word-motion 回退，避免干扰文本编辑。
- 各事件渲染函数通过 `collab_event` 统一构造带标题和缩进详情的历史单元格。
