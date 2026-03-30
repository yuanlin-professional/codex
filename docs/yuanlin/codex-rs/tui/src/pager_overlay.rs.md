# `pager_overlay.rs` — 全屏翻页覆盖层

## 功能概述

实现 TUI 的翻页式覆盖层界面，包括 Ctrl+T 触发的 **TranscriptOverlay**（对话历史全屏查看）
和通用的 **StaticOverlay**（静态内容查看）。支持 vim 风格的滚动导航（j/k/上/下/PgUp/PgDn/
Home/End/Ctrl+F/B/D/U），百分比进度指示，以及内容渲染缓存和懒计算。
TranscriptOverlay 还支持实时尾部追踪（live tail）和历史单元格高亮编辑。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Overlay` | enum | 覆盖层类型：Transcript 或 Static |
| `TranscriptOverlay` | struct | 对话历史覆盖层，含实时尾部和单元格高亮 |
| `StaticOverlay` | struct | 静态内容覆盖层 |
| `PagerView` | struct | 通用翻页视图，管理滚动偏移和渲染 |
| `CachedRenderable` | struct | 缓存 desired_height 的渲染代理 |
| `LiveTailKey` | struct | 实时尾部缓存键（宽度、修订、动画 tick） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `sync_live_tail` | `(&mut self, width, key, compute_fn)` | 同步活跃单元格的实时尾部 |
| `insert_cell` | `(&mut self, cell)` | 插入已提交的历史单元格 |
| `replace_cells` | `(&mut self, cells)` | 替换所有已提交单元格（回滚用） |
| `set_highlight_cell` | `(&mut self, Option<usize>)` | 设置高亮编辑的单元格索引 |

## 依赖关系

- **引用的模块**: `crate::chatwidget`, `crate::history_cell`, `crate::key_hint`, `crate::render`, `crate::style`, `crate::tui`

## 实现备注

- 实时尾部通过缓存键比较实现按需重算，避免每帧重建换行后的行数据。
- 底部跟随（follow bottom）：当用户在底部时，新内容插入后自动保持在底部。
- 用户手动滚动后，新内容不改变滚动位置。
- `render_offset_content` 通过中间缓冲区实现部分内容的偏移渲染。
