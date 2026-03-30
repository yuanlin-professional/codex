# `renderable.rs` — 可渲染组件抽象和布局原语

## 功能概述

本模块定义了 TUI 的核心渲染抽象 `Renderable` trait 及其多种布局组合器。`Renderable` 要求实现 `render` 和 `desired_height`，可选实现 `cursor_pos`。布局原语包括 `ColumnRenderable`（垂直堆叠）、`RowRenderable`（水平排列）、`FlexRenderable`（弹性布局）和 `InsetRenderable`（内边距包装）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Renderable` | trait | 可渲染组件核心 trait：render + desired_height + cursor_pos |
| `RenderableItem` | enum | 渲染项包装：Owned 或 Borrowed |
| `ColumnRenderable` | struct | 垂直堆叠布局 |
| `RowRenderable` | struct | 水平排列布局（固定宽度子项） |
| `FlexRenderable` | struct | 弹性因子布局（类似 Flutter Flex） |
| `InsetRenderable` | struct | 内边距包装布局 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ColumnRenderable::push` | `(&mut self, child)` | 添加子项到垂直布局 |
| `FlexRenderable::push` | `(&mut self, flex, child)` | 添加带弹性因子的子项 |
| `RenderableExt::inset` | `(self, insets) -> RenderableItem` | 扩展方法：为任意 Renderable 添加内边距 |

## 依赖关系

- **引用的 crate**: `ratatui`, `crate::render::Insets`
- **被引用方**: 几乎所有 TUI UI 组件

## 实现备注

- 为 `&str`、`String`、`Span`、`Line`、`Paragraph`、`Option<R>`、`Arc<R>` 和 `()` 提供了 `Renderable` 实现
- FlexRenderable 的空间分配算法受 Flutter 的 Flex widget 启发
- ColumnRenderable 的 cursor_pos 返回第一个有光标位置的子项
