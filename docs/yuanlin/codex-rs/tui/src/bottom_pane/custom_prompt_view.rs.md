# `custom_prompt_view.rs` -- 自定义提示输入视图

## 功能概述
该文件实现了一个最小化的多行文本输入视图（CustomPromptView），用于收集用户的自定义审查指令。当用户需要为特定操作提供附加说明时显示。支持标题、占位符文本、可选的上下文标签，以及 Enter 提交和 Esc 取消操作。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `PromptSubmitted` | 类型别名 | `Box<dyn Fn(String) + Send + Sync>`，提交时的回调函数 |
| `CustomPromptView` | 结构体 | 自定义提示视图，包含标题、占位符、文本区域和提交回调 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(title, placeholder, context_label, on_submit) -> Self` | 创建新的自定义提示视图 |
| `handle_key_event` | `fn handle_key_event(&mut self, key_event)` | Enter 提交、Esc 取消、其他键路由到文本区域 |
| `render` | `fn render(&self, area, buf)` | 渲染标题行、上下文标签、输入区域和提示行 |
| `cursor_pos` | `fn cursor_pos(&self, area) -> Option<(u16, u16)>` | 返回光标位置用于终端显示 |

## 依赖关系
- **引用的 crate**: `crossterm`、`ratatui`
- **被引用方**: 通过 `mod.rs` 的 `pub mod custom_prompt_view` 公开

## 实现备注
- 输入区域高度自适应，最大 8 行
- 左侧显示蓝色竖线（`▌`）作为视觉标识
- Enter 不带修饰键提交，带修饰键的 Enter 插入换行
- 支持粘贴文本处理
