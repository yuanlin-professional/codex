# `request_user_input/render.rs` -- 用户输入请求叠加层渲染

## 功能概述
该文件负责用户输入请求叠加层的渲染逻辑，包括主表单渲染和未回答确认对话框渲染。实现了 `Renderable` trait 的 `desired_height`、`render` 和 `cursor_pos` 方法。渲染内容包括进度行、问题文本、选项列表（支持底部对齐）、备注输入区域和页脚提示行。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `UnansweredConfirmationData` | 结构体 | 未回答确认对话框的渲染数据 |
| `UnansweredConfirmationLayout` | 结构体 | 未回答确认对话框的布局数据 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `render_ui` | `fn render_ui(&self, area, buf)` | 主渲染入口 |
| `render_unanswered_confirmation` | `fn render_unanswered_confirmation(&self, area, buf)` | 渲染未回答确认对话框 |
| `render_notes_input` | `fn render_notes_input(&self, area, buf)` | 渲染备注输入区域（支持密码掩码） |
| `render_rows_bottom_aligned` | `fn render_rows_bottom_aligned(...)` | 底部对齐的选项列表渲染 |
| `truncate_line_word_boundary_with_ellipsis` | `fn truncate_line_word_boundary_with_ellipsis(...)` | 在单词边界截断行并添加省略号 |

## 依赖关系
- **引用的 crate**: `ratatui`、`unicode_width`
- **被引用方**: `RequestUserInputOverlay` 的 Renderable 实现

## 实现备注
- 选项列表使用底部对齐渲染，确保页脚间距稳定
- 已回答问题文本正常显示，未回答问题文本显示为青色
- 页脚提示使用单词边界截断防止溢出
- 最小叠加层高度为 8 行
