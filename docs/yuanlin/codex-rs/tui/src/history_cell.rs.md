# `history_cell.rs` — 对话历史单元定义与渲染

## 功能概述

本文件定义了 TUI 对话历史中各类显示单元的 trait 和具体实现。`HistoryCell` 是对话 UI 中的基本显示单位，既表示已提交的对话记录条目，也临时表示正在流式传输中的活跃单元。

文件体量非常大，实现了多种历史单元类型：用户消息、Agent 消息、命令执行结果、文件变更、MCP 工具调用、会话信息、计划步骤、错误/警告消息等，每种单元都包含自己的渲染逻辑（`display_lines` 方法）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `HistoryCell` | trait | 历史单元特征，定义 `display_lines`、`as_any`、`transcript_animation_tick` 等方法 |
| `UserHistoryCell` | 结构体 | 用户消息单元 |
| `AgentMessageCell` | 结构体 | Agent 消息单元 |
| `SessionInfoCell` | 结构体 | 会话信息单元（会话开始标记） |
| `PlainHistoryCell` | 结构体 | 纯文本历史单元 |
| `UpdateAvailableHistoryCell` | 结构体 | 更新可用提示单元 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（FileChange 等协议类型）、`ratatui`（渲染类型）、`image`、大量内部模块
- **被引用方**: `chatwidget.rs`（构建和更新历史单元）、`app_backtrack.rs`（修剪对话记录）

## 实现备注

- `HistoryCell` 使用 `dyn Any` 支持运行时类型检查，用于回溯功能中区分用户消息和会话信息单元。
- 命令执行单元支持输出折叠、spinner 动画和行数限制。
- 文件变更单元集成了差异渲染器。
- 支持 transcript 动画 tick 的单元会触发覆盖层缓存刷新。
