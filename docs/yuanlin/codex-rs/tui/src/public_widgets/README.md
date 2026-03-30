# src/public_widgets/

## 功能概述

`public_widgets/` 模块提供对外公开的可复用 UI 组件。这些组件被设计为可以在其他 crate（如 `codex-cloud-tasks`）中使用的稳定接口。

目前包含 `ComposerInput`，这是内部 `ChatComposer` 的精简公开封装，提供多行输入、粘贴启发式、Enter 提交和 Shift+Enter 换行等成熟的输入行为。

## 目录结构

```
public_widgets/
├── mod.rs              # 模块入口
└── composer_input.rs   # ComposerInput 公开输入组件
```

## 核心接口/API

| 类型/函数 | 说明 |
|-----------|------|
| `ComposerInput` | 公开的可复用文本输入组件，封装 `ChatComposer` |
| `ComposerInput::new()` | 创建新的输入组件 |
| `ComposerInput::is_empty()` | 检查输入是否为空 |
| `ComposerInput::clear()` | 清空输入内容 |
| `ComposerInput::feed_key()` | 输入按键事件 |
| `ComposerInput::render()` | 渲染输入框 |
| `ComposerAction` | 输入动作枚举 |
| `ComposerAction::Submitted(String)` | 用户提交了文本 |
| `ComposerAction::None` | 无提交动作 |
