# `list_selection_view.rs` -- 通用列表选择视图

## 功能概述
该文件实现了一个功能丰富的通用列表选择视图（ListSelectionView），是底部面板中多种选择弹出窗口的核心组件。支持搜索过滤、键盘导航、侧边面板（side-by-side 或 stacked 布局）、列宽模式（自动/固定）、禁用项跳过、回调（选择变更/取消）等高级功能。被模型选择、审批模式选择、主题选择等多处使用。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SelectionItem` | 结构体 | 选择列表中的一行数据，包含名称、描述、操作回调、快捷键等 |
| `SelectionViewParams` | 结构体 | 构造 ListSelectionView 的配置参数 |
| `ListSelectionView` | 结构体 | 运行时列表选择视图，管理过滤索引映射、滚动和选择 |
| `SideContentWidth` | 枚举 | 侧边面板宽度模式：Fixed(u16) 或 Half |
| `ColumnWidthMode` | 枚举 | 列宽模式：AutoVisible、AutoAllRows、Fixed |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(params, app_event_tx) -> Self` | 从参数构造视图，初始化过滤和选择 |
| `apply_filter` | `fn apply_filter(&mut self)` | 应用搜索过滤并保持选择状态 |
| `accept` | `fn accept(&mut self)` | 确认当前选择，执行关联操作 |
| `render` | `fn render(&self, area, buf)` | 渲染完整视图：头部、搜索栏、列表行、侧边面板、页脚 |
| `side_layout_width` | `fn side_layout_width(&self, content_width) -> Option<u16>` | 计算侧边面板宽度 |

## 依赖关系
- **引用的 crate**: `crossterm`、`ratatui`、`itertools`、`unicode_width`
- **被引用方**: `BottomPane` 的 `show_selection_view` 方法、`ApprovalOverlay` 内部使用

## 实现备注
- 侧边面板支持两种布局：宽终端使用 side-by-side，窄终端使用 stacked fallback
- 禁用项在导航时自动跳过（skip_disabled_up/down）
- 数字键快速选择仅在非搜索模式下可用
- 三种列宽模式影响描述列的对齐和滚动稳定性
