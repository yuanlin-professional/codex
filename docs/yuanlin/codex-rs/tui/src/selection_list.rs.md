# `selection_list.rs` — 选择列表行渲染

## 功能概述

提供 `selection_option_row` 函数，用于渲染带编号的选择菜单行。
选中项用 `›` 前缀和青色样式标识，未选中项使用普通样式。
支持可选的 dim 样式用于禁用项。被多个选择器界面（模型迁移、更新提示等）复用。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `selection_option_row` | `(index, label, is_selected) -> Box<dyn Renderable>` | 生成选择行 |
| `selection_option_row_with_dim` | `(index, label, selected, dim) -> Box<dyn Renderable>` | 带 dim 的变体 |

## 依赖关系

- **引用的模块**: `crate::render::renderable`
