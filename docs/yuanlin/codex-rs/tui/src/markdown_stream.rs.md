# `markdown_stream.rs` — 流式 Markdown 增量渲染收集器

## 功能概述

实现 `MarkdownStreamCollector`，用于在模型流式输出时增量渲染 Markdown。
收集器缓冲文本增量（delta），仅在遇到换行符时提交已完成的逻辑行，
确保流式渲染与完整渲染结果一致。支持在流结束时 finalize 剩余未提交内容。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `MarkdownStreamCollector` | struct | 基于换行的增量 Markdown 渲染收集器 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `(width, cwd) -> Self` | 创建收集器，快照 cwd |
| `push_delta` | `(&mut self, delta)` | 追加文本增量 |
| `commit_complete_lines` | `(&mut self) -> Vec<Line>` | 提交已完成的行 |
| `finalize_and_drain` | `(&mut self) -> Vec<Line>` | 结束流并排出所有剩余行 |
| `simulate_stream_markdown_for_tests` | `(deltas, finalize) -> Vec<Line>` | 测试辅助函数 |

## 依赖关系

- **引用的模块**: `crate::markdown`, `crate::render::line_utils`

## 实现备注

- 仅在缓冲区末尾有换行时才认为最后一行完成，避免未完成行闪烁。
- 维护 `committed_line_count` 避免重复输出已提交行。
- 测试覆盖了标题分块、块引用样式保持、深层嵌套列表标记颜色、UTF-8 边界安全等场景。
