# `mod.rs` — 渲染模块入口

## 功能概述

渲染模块的入口文件，定义了 `Insets` 结构体（用于矩形区域的内边距计算）和 `RectExt` trait（为 `ratatui::layout::Rect` 添加 `inset` 方法）。组织导出 `highlight`、`line_utils` 和 `renderable` 三个子模块。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Insets` | struct | 四边内边距（top、left、bottom、right） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Insets::tlbr` | `(top, left, bottom, right) -> Self` | 分别指定四边内边距 |
| `Insets::vh` | `(v, h) -> Self` | 垂直和水平对称内边距 |
| `RectExt::inset` | `(&self, insets) -> Rect` | 返回应用内边距后的内部矩形 |

## 依赖关系

- **引用的 crate**: `ratatui::layout::Rect`
- **被引用方**: `renderable` 模块中的 `InsetRenderable`，各 UI 组件
