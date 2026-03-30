# `request_user_input/layout.rs` -- 用户输入请求叠加层布局计算

## 功能概述
该文件负责用户输入请求叠加层（RequestUserInputOverlay）的布局计算。根据可用高度、宽度和内容类型（有无选项、是否显示备注区域），计算各区域（进度行、问题区域、选项区域、备注区域、页脚）的垂直分配。支持空间紧张时的优雅退化——逐步缩减选项高度、隐藏间距和截断问题文本。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `LayoutSections` | 结构体 | 布局结果，包含各区域的 Rect 和换行后的问题文本 |
| `LayoutPlan` | 结构体 | 中间布局计划，记录各区域高度和间距 |
| `OptionsLayoutArgs` | 结构体 | 有选项时的布局参数 |
| `OptionsHeights` | 结构体 | 选项区域的首选高度和完整高度 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `layout_sections` | `fn layout_sections(&self, area) -> LayoutSections` | 主入口：计算所有区域的布局 |
| `layout_with_options` | `fn layout_with_options(...)` | 有选项时的布局逻辑 |
| `layout_without_options` | `fn layout_without_options(...)` | 无选项时的布局逻辑 |
| `build_layout_areas` | `fn build_layout_areas(...)` | 从高度计划构建最终 Rect |

## 依赖关系
- **引用的 crate**: `ratatui`（Rect）
- **被引用方**: `RequestUserInputOverlay` 的 render 方法

## 实现备注
- 有选项时：先分配进度行和页脚，再在问题和选项间分配剩余空间
- 无选项时：更简单的线性布局，备注区域获得所有剩余空间
- 间距策略：有备注时减少间距（1 行），无备注时保持标准间距
