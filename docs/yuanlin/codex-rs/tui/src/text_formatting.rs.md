# `text_formatting.rs` — 文本格式化工具集

## 功能概述

提供多种文本处理工具函数：首字母大写、JSON 紧凑格式化、基于字素的文本截断、
路径中间截断、以及英语标点连接（"apple, banana and cherry"）。
这些函数被 TUI 的多个组件使用，用于工具结果显示、状态行路径显示等场景。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `capitalize_first` | `(input) -> String` | 首字母大写 |
| `format_and_truncate_tool_result` | `(text, max_lines, width) -> String` | 格式化并截断工具结果 |
| `format_json_compact` | `(text) -> Option<String>` | JSON 紧凑单行格式化（带空格） |
| `truncate_text` | `(text, max_graphemes) -> String` | 按字素截断并添加 "..." |
| `center_truncate_path` | `(path, max_width) -> String` | 路径中间截断（保留首尾段） |
| `proper_join` | `(items) -> String` | 英语标点连接列表 |

## 依赖关系

- **引用的 crate**: `unicode_segmentation`, `unicode_width`, `serde_json`

## 实现备注

- `format_json_compact` 将 pretty JSON 压缩为带空格的单行，适配 ratatui 仅在空白处换行的限制。
- `center_truncate_path` 使用组合搜索策略，优先保留前缀段和最后两个后缀段，
  必要时对个别段落进行前端截断。
- `truncate_text` 使用 Unicode 字素索引避免在多码点字符中间截断。
