# `textarea.rs` -- 多行文本编辑器核心

## 功能概述
该文件实现了 TUI 的核心多行文本编辑器（TextArea），管理可编辑文本、占位符元素、光标/换行状态和单条目 kill 缓冲区。支持完整的文本编辑功能：字符插入/删除、单词导航、行首/行尾跳转、Ctrl+K 删除到行尾、Ctrl+Y 粘贴 kill 缓冲区、文本元素（如图片占位符）的范围追踪等。这是底部面板中最底层的文本编辑组件，超过 2400 行代码。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `TextArea` | 结构体 | 多行文本编辑器，持有文本内容、光标位置、文本元素、换行缓存等 |
| `TextAreaState` | 结构体 | 有状态渲染所需的渲染状态（垂直滚动偏移） |
| `TextElementSnapshot` | 结构体 | 文本元素的快照，用于外部同步 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `input` | `fn input(&mut self, key_event: KeyEvent)` | 处理键盘输入 |
| `insert_str` | `fn insert_str(&mut self, s: &str)` | 在光标位置插入字符串 |
| `text` | `fn text(&self) -> &str` | 获取当前文本 |
| `set_text` | `fn set_text(&mut self, text: String)` | 替换全部文本 |
| `cursor_pos_with_state` | `fn cursor_pos_with_state(...)` | 计算光标的终端坐标位置 |
| `desired_height` | `fn desired_height(&self, width: u16) -> u16` | 计算所需行数 |

## 依赖关系
- **引用的 crate**: `crossterm`、`ratatui`、`textwrap`、`unicode_segmentation`、`unicode_width`、`codex_protocol`
- **被引用方**: `ChatComposer`、`CustomPromptView`、`FeedbackNoteView`、`RequestUserInputOverlay` 等

## 实现备注
- 全缓冲区替换 API 保持 kill 缓冲区不变，支持 Ctrl+Y 跨清空操作
- 文本元素通过字节范围追踪，插入/删除时自动调整范围
- 使用 textwrap 进行软换行计算
- 支持通过 `render_with_mask` 进行密码掩码渲染
- 不实现 Emacs 风格的多条目 kill ring，只保留最近一次删除
