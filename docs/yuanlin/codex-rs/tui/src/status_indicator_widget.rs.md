# `status_indicator_widget.rs` — 任务状态指示器组件

## 功能概述

在 composer 上方渲染一行实时任务状态，包含动画旋转器、闪烁标题文本、
已用时间、中断提示和可选的内联消息。支持可折叠的多行详情文本。
计时器支持暂停/恢复以准确跟踪实际工作时间。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `StatusIndicatorWidget` | struct | 状态指示器组件 |
| `StatusDetailsCapitalization` | enum | 详情首字母大写策略 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `(tx, frame_req, animations) -> Self` | 创建指示器 |
| `update_header` | `(&mut self, header)` | 更新动画标题 |
| `update_details` | `(&mut self, details, cap, max_lines)` | 更新详情文本 |
| `update_inline_message` | `(&mut self, message)` | 更新内联消息 |
| `pause_timer/resume_timer` | `(&mut self)` | 暂停/恢复计时 |
| `fmt_elapsed_compact` | `(secs) -> String` | 格式化时间（如 "1m 05s"） |

## 依赖关系

- **引用的模块**: `crate::exec_cell::spinner`, `crate::shimmer`, `crate::key_hint`, `crate::wrapping`, `crate::text_formatting`, `crate::line_truncation`

## 实现备注

- 动画启用时每 32ms 调度一帧重绘。
- 详情文本通过 `word_wrap_lines` 换行，超出 `max_lines` 时末行添加省略号。
- 中断提示可通过 `set_interrupt_hint_visible` 控制显隐。
