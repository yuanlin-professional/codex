# `chatwidget.rs` — TUI 主聊天界面组件

## 功能概述

本文件是 Codex TUI 最核心的 widget 模块，定义了 `ChatWidget` 结构体。它负责消费协议事件、构建和更新历史单元（HistoryCell）、驱动主视口和覆盖层 UI 的渲染。ChatWidget 管理已提交的对话记录单元和正在进行的活跃单元，处理流式输出的合并与显示。

文件体量非常大（超过 10000 行），实现了完整的聊天交互逻辑，包括：消息渲染、命令提交、审批流程、实时音频管理、MCP 服务器状态跟踪、模型切换、协作模式管理、对话记录覆盖层的活跃单元缓存同步等。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ChatWidget` | 结构体 | 主聊天组件，持有会话状态、底部面板、模型目录、活跃单元等 |
| `ExternalEditorState` | 结构体 | 外部编辑器状态 |
| `ReplayKind` | 枚举 | 回放类型 |
| `ThreadInputState` | 结构体 | 线程输入状态 |

## 依赖关系

- **引用的 crate**: `codex_protocol`、`codex_app_server_protocol`、`codex_core`、`ratatui` 及大量内部模块
- **被引用方**: `app.rs`（App 持有 ChatWidget 并在事件循环中驱动其更新和渲染）

## 实现备注

- 活跃单元（active_cell）是流式输出期间可变的，与已提交的 transcript_cells 分离管理。
- 对话记录覆盖层通过 `active_cell_transcript_key()` 和 `active_cell_transcript_lines()` 实现缓存同步。
- 底部面板暴露统一的"任务运行中"指标，由 agent_turn_running 和 mcp_startup_status 两个独立生命周期驱动。
