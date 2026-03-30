# src/bottom_pane/request_user_input/

## 功能概述

`request_user_input/` 模块实现了用户输入请求覆盖层的状态机。当 AI 助手需要向用户询问信息时（例如选择选项或输入自由文本备注），此覆盖层会显示在底部面板中。

核心行为：
- 每个问题可以通过选择一个选项和/或提供备注来回答
- 备注按问题独立存储，作为额外答案追加
- 在选项聚焦时输入文字会自动跳转到备注输入框
- Enter 键前进到下一个问题；最后一个问题提交所有答案
- 纯自由文本问题在为空时提交空答案列表

## 目录结构

```
request_user_input/
├── mod.rs          # 状态机核心逻辑，键盘事件处理
├── layout.rs       # 布局计算
├── render.rs       # 渲染逻辑
└── snapshots/      # 渲染快照测试
```

## 核心接口/API

| 类型/函数 | 说明 |
|-----------|------|
| `RequestUserInputOverlay` | 用户输入请求覆盖层，实现 `BottomPaneView` 接口 |
| `handle_key_event()` | 处理键盘输入，驱动问题导航和答案提交 |
| `render()` | 渲染当前问题、选项列表和备注输入框 |
