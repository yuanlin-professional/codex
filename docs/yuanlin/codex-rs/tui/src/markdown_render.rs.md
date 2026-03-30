# `markdown_render.rs` — Markdown 到 ratatui 富文本渲染器

## 功能概述

使用 `pulldown_cmark` 解析 Markdown 事件流，将其转换为带样式的 ratatui `Text`。
支持标题、列表（有序/无序/嵌套）、块引用、行内代码、强调/加粗/删除线、链接、
代码块（含语法高亮）、水平分割线以及 HTML 透传。对本地文件链接（`file://`、
绝对路径、`~/` 等）进行特殊处理：显示目标路径而非 Markdown 标签，并可根据
工作目录缩短绝对路径。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `MarkdownStyles` | struct | 各 Markdown 元素的 ratatui 样式配置 |
| `IndentContext` | struct | 缩进栈条目，跟踪列表/块引用前缀 |
| `Writer` | struct | 核心状态机，遍历 pulldown-cmark 事件并输出 `Text` |
| `LinkState` | struct | 链接渲染状态，区分远程 URL 和本地文件链接 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `render_markdown_text` | `(input) -> Text` | 使用进程 cwd 渲染 Markdown |
| `render_markdown_text_with_width` | `(input, width) -> Text` | 指定宽度渲染 |
| `render_markdown_text_with_width_and_cwd` | `(input, width, cwd) -> Text` | 指定宽度和 cwd 渲染 |
| `is_local_path_like_link` | `(dest_url) -> bool` | 判断链接目标是否为本地路径 |
| `render_local_link_target` | `(dest_url, cwd) -> Option<String>` | 生成本地链接的显示文本 |
| `parse_local_link_target` | `(dest_url) -> Option<(path, suffix)>` | 解析本地链接为路径+位置后缀 |

## 依赖关系

- **引用的 crate**: `pulldown_cmark`, `ratatui`, `regex_lite`, `url`, `dirs`, `codex_utils_string`
- **引用的模块**: `crate::render::highlight`, `crate::render::line_utils`, `crate::wrapping`

## 实现备注

- Writer 使用事件驱动状态机模式，维护缩进栈、内联样式栈和链接状态。
- 代码块在已知语言时缓冲内容，在 `end_codeblock` 时批量语法高亮。
- 本地文件链接在渲染时抑制 Markdown 标签文本，改为显示解析后的目标路径。
- 行自动换行通过 `adaptive_wrap_line` 实现，代码块内不换行以保留空白。
- 路径规范化仅做词法处理（正斜杠统一、`~/` 展开），不访问文件系统。
