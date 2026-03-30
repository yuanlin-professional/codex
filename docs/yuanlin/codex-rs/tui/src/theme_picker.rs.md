# `theme_picker.rs` — 语法主题选择器

## 功能概述

构建 `/theme` 命令的主题选择器对话框。列出所有内置主题和 `{CODEX_HOME}/themes/` 下的
自定义 `.tmTheme` 文件。支持实时预览（光标移动时切换语法主题）、取消时恢复原主题、
确认时通过 `ConfigEditsBuilder` 持久化选择。提供宽窄两种 diff 预览渲染模式，
根据终端宽度自适应切换。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ThemePreviewWideRenderable` | struct | 侧边栏宽预览（垂直居中的 8 行 Rust diff） |
| `ThemePreviewNarrowRenderable` | struct | 堆叠窄预览（4 行紧凑 diff） |
| `PreviewRow` / `PreviewDiffKind` | struct/enum | 预览行数据和 diff 类型 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_theme_picker_params` | `(current, home, width) -> SelectionViewParams` | 构建选择器参数 |
| `render_preview` | `(area, buf, rows, center, inset)` | 渲染 diff 预览 |

## 依赖关系

- **引用的模块**: `crate::app_event`, `crate::bottom_pane`, `crate::diff_render`, `crate::render::highlight`, `crate::status`

## 实现备注

- 宽预览需要侧面板 >= 44 列且列表 >= 40 列才启用，否则回退到窄预览。
- 副标题优先显示自定义主题目录路径（使用 ~ 缩写），不够宽时回退到通用提示。
- 通过 `on_selection_changed` 回调实现光标移动时的实时主题切换。
