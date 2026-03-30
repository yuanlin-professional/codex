# `markdown_render_tests.rs` — 测试模块

## 测试覆盖范围

对 `markdown_render.rs` 核心渲染逻辑的全面测试，覆盖段落、标题、块引用、列表、
内联样式、链接、代码块、HTML、水平分割线以及本地文件链接路径解析等场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `empty` | 空输入返回空 Text |
| `paragraph_single/soft_break/multiple` | 单段落、软换行、多段落渲染 |
| `headings` | H1-H6 各级标题样式 |
| `blockquote_*`（多个） | 块引用的各种嵌套组合（单行、多段、嵌套、含列表、含代码块） |
| `list_*`（多个） | 有序/无序/嵌套/松散列表、五级混合嵌套 |
| `inline_code/strong/emphasis/strikethrough` | 行内样式 |
| `link` | URL 链接显示目的地 |
| `file_link_*`（多个） | 本地文件链接：隐藏目的地、追加行号/范围、hash 锚点、cwd 外路径保留 |
| `code_block_*`（多个） | 有语言/无语言/缩进代码块、语法高亮、嵌套反引号 |
| `horizontal_rule_renders_em_dashes` | 水平分割线渲染为 em-dash |
| `html_*`（多个） | 内联 HTML 和块级 HTML 的透传渲染 |
| `markdown_render_complex_snapshot` | 综合 Markdown 快照测试 |
