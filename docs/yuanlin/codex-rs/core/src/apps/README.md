# apps/ -- 应用渲染

## 功能概述

`apps/` 是一个轻量级模块，负责将 Codex 应用（Apps/Connectors）的信息渲染为 developer message 中的描述段落。当会话启用了应用连接器时，该模块将连接器的描述信息格式化并注入到发送给模型的上下文中，让模型了解可用的外部应用能力。

## 目录结构

```
apps/
├── mod.rs       # 模块入口，导出 render 子模块
└── render.rs    # render_apps_section()：应用描述段渲染
```

## 依赖关系

- **上游依赖**：`codex-app-server-protocol`（AppInfo 类型）
- **内部依赖**：无直接内部依赖
- **被依赖方**：`codex/`（Session 构建 developer message 时调用 render_apps_section）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `render_apps_section()` | 将应用连接器信息渲染为模型可见的描述文本 |
