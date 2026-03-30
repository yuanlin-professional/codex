# `markdown.rs` — Markdown 追加渲染入口

## 功能概述

提供 `append_markdown` 函数，将 Markdown 源文本渲染为 ratatui `Line` 并追加到输出向量中。
该函数接受可选的终端宽度和工作目录参数，以便正确渲染本地文件链接的相对路径。
底层委托给 `markdown_render::render_markdown_text_with_width_and_cwd`。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `append_markdown` | `(markdown_source, width, cwd, lines)` | 渲染 Markdown 并追加行到输出 |

## 依赖关系

- **引用的 crate**: `ratatui`, `pretty_assertions`（测试）
- **引用的模块**: `crate::markdown_render`, `crate::render::line_utils`

## 实现备注

模块包含内联测试，验证引用标记、缩进代码块、有序列表等渲染场景的正确性。
