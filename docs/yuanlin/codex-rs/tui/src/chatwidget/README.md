# src/chatwidget/

## 功能概述

`chatwidget/` 是 TUI 聊天界面的核心组件目录。`ChatWidget` 消费协议事件，构建和更新历史单元格（`HistoryCell`），并驱动主视区和覆盖层 UI 的渲染。

UI 包含已提交的转录单元格（已完成的 `HistoryCell`）和正在传输中的活动单元格（`active_cell`），后者在流式传输时可原地变化。转录覆盖层（`Ctrl+T`）渲染已提交单元格加上一个缓存的实时尾部，以便正在执行的工具调用能立即可见。

底部面板暴露一个"任务运行中"指示器，驱动旋转动画和中断提示。

## 目录结构

```
chatwidget/
├── interrupts.rs       # 中断队列管理（审批、执行开始/结束、MCP 调用）
├── plugins.rs          # 插件管理 UI（安装、卸载、列表、详情）
├── realtime.rs         # 实时语音对话 UI 状态和控制
├── session_header.rs   # 会话头部（模型名称显示）
├── skills.rs           # 技能（Skills）管理 UI
├── status_surfaces.rs  # 状态行和终端标题渲染辅助
├── tests.rs            # ChatWidget 单元测试
└── snapshots/          # 渲染快照测试
```

## 核心接口/API

| 类型/函数 | 说明 |
|-----------|------|
| `ChatWidget` | 聊天界面主组件，管理历史单元格、活动单元格、流式状态和 UI 覆盖层 |
| `QueuedInterrupt` | 排队的中断事件枚举（执行审批、文件变更审批、MCP 请求等） |
| `RealtimeConversationPhase` | 实时语音对话阶段（Inactive/Starting/Active/Stopping） |
| `RealtimeConversationUiState` | 实时语音对话 UI 状态 |
| `SessionHeader` | 会话头部组件，显示当前模型名称 |
| `TerminalTitleStatusKind` | 终端标题状态种类枚举（Working/Thinking/Undoing 等） |
| `ChatWidget::open_skills_list()` | 打开技能列表选择器 |
| `ChatWidget::handle_plugin_*()` | 插件安装/卸载/列表等操作处理 |
