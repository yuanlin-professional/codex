# `selection_popup_common.rs` -- 选择弹出窗口共享渲染逻辑

## 功能概述
该文件提供了选择弹出窗口的共享渲染基础设施，包括行数据模型（GenericDisplayRow）、菜单表面渲染（menu_surface）、行渲染（单行/多行/固定列宽/稳定列宽）、行高度测量以及文本换行等功能。是 CommandPopup、FileSearchPopup、ListSelectionView、SkillPopup 等所有选择型弹出窗口的共享底层。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `GenericDisplayRow` | 结构体 | 一行的渲染数据，包含名称、前缀 spans、匹配索引、描述、分类标签等 |
| `ColumnWidthMode` | 枚举 | 列宽模式：AutoVisible、AutoAllRows、Fixed |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `render_rows` | `fn render_rows(...) -> u16` | 使用 AutoVisible 模式渲染行列表 |
| `render_rows_single_line` | `fn render_rows_single_line(...) -> u16` | 单行渲染（截断不换行） |
| `measure_rows_height` | `fn measure_rows_height(...) -> u16` | 测量所需的终端行数 |
| `render_menu_surface` | `fn render_menu_surface(area, buf) -> Rect` | 绘制菜单表面背景并返回内缩区域 |
| `wrap_styled_line` | `fn wrap_styled_line(line, width) -> Vec<Line>` | 保持 span 样式的换行 |
| `build_full_line` | `fn build_full_line(row, desc_col) -> Line` | 构建包含名称和描述的完整行 |

## 依赖关系
- **引用的 crate**: `ratatui`、`unicode_width`、`textwrap`
- **被引用方**: 所有选择型弹出窗口组件

## 实现备注
- 名称列宽有上限：自动模式下不超过总宽度的 70%
- 固定模式使用 30/70 分割（名称/描述）
- 模糊匹配索引通过 bold 样式高亮显示
- 禁用行显示为 dim 样式，选中行显示为 cyan+bold
- 换行后的续行使用 `wrap_indent` 控制缩进
