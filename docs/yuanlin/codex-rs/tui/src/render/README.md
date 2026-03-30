# src/render/

## 功能概述

`render/` 模块提供 TUI 的渲染基础设施，包括语法高亮引擎、行级工具函数和可渲染组件抽象。

核心能力：
- **语法高亮**（`highlight.rs`）：基于 syntect + two_face 提供约 250 种语言的语法高亮和 32 种内置颜色主题。支持运行时主题切换和自定义 `.tmTheme` 加载。
- **可渲染抽象**（`renderable.rs`）：`Renderable` trait 定义了统一的组件渲染接口，支持高度计算、光标定位和灵活布局。
- **行工具**（`line_utils.rs`）：行级操作辅助函数，如行克隆、行前缀添加、空行检测等。
- **布局工具**（`mod.rs`）：`Insets` 内边距和 `RectExt` 矩形扩展。

## 目录结构

```
render/
├── mod.rs          # 模块入口，Insets 和 RectExt 定义
├── highlight.rs    # 语法高亮引擎（syntect + two_face）
├── line_utils.rs   # 行级工具函数
├── renderable.rs   # Renderable trait 和组件抽象
└── snapshots/      # 渲染快照测试
```

## 核心接口/API

| 类型/函数 | 说明 |
|-----------|------|
| `Renderable` trait | 统一渲染接口：`render()`、`desired_height()`、`cursor_pos()` |
| `RenderableItem` | 拥有/借用的可渲染项枚举 |
| `FlexRenderable` | 灵活渲染抽象 |
| `ColumnRenderable` | 列渲染抽象 |
| `Insets` | 内边距描述（top/left/bottom/right） |
| `RectExt::inset()` | 矩形内缩扩展方法 |
| `highlight_bash_to_lines()` | 将 bash 命令高亮渲染为 ratatui Line 列表 |
| `set_theme_override()` | 启动时设置语法高亮主题（OnceLock，仅可调用一次） |
| `set_syntax_theme()` / `current_syntax_theme()` | 运行时切换/查询当前主题 |
| `validate_theme_name()` | 验证主题名称是否有效 |
| `line_to_static()` | 将借用的 Line 克隆为 `'static` 生命周期 |
| `push_owned_lines()` | 追加拥有的行副本 |
| `is_blank_line_spaces_only()` | 检测空白行 |
| `prefix_lines()` | 为行添加前缀 |
