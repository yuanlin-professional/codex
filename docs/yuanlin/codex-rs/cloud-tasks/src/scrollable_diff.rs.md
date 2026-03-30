# `scrollable_diff.rs` — 可滚动 diff/文本视图组件

## 功能概述
实现一个简单的本地可滚动文本视图组件，用于在 TUI 中显示 diff 或消息文本。管理原始行数据、宽度自适应的自动换行缓存和滚动状态。支持 Unicode 宽度感知的软换行算法。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ScrollViewState` | 结构体 | 滚动位置和几何信息：scroll、viewport_h、content_h |
| `ScrollableDiff` | 结构体 | 可滚动 diff 视图，持有原始行、换行缓存和滚动状态 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `set_content` | `fn(&mut self, lines)` | 替换原始内容行 |
| `set_width` | `fn(&mut self, width)` | 设置换行宽度并重新计算 |
| `scroll_by` | `fn(&mut self, delta)` | 按增量滚动 |
| `percent_scrolled` | `fn(&self) -> Option<u8>` | 返回滚动百分比 |

## 依赖关系
- **引用的 crate**: `unicode_width`
- **被引用方**: `app.rs`, `ui.rs`
