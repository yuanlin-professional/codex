# `live_wrap.rs` — 增量文本换行构建器

## 功能概述

本文件实现了 `RowBuilder` 结构体，用于将流式输入文本增量换行为固定宽度的可视行。它支持分片输入（保持分片无关性）、显式换行符处理、宽度变更时的重新换行、以及提交就绪行的排出（drain）功能。

这是流式 Agent 输出实时渲染的基础组件，能够在文本片段逐步到达时持续生成正确换行的显示行。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Row` | 结构体 | 单个可视行：文本内容和是否为显式换行 |
| `RowBuilder` | 结构体 | 增量换行构建器，持有目标宽度、当前行缓冲和已产生的行 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(target_width) -> Self` | 创建指定宽度的构建器 |
| `push_fragment` | `fn(&mut self, fragment)` | 推入文本片段，自动处理换行和硬换行 |
| `set_width` | `fn(&mut self, width)` | 更改目标宽度并重新换行所有内容 |
| `drain_rows` | `fn(&mut self) -> Vec<Row>` | 排出所有已产生的行 |
| `drain_commit_ready` | `fn(&mut self, max_keep) -> Vec<Row>` | 排出超出保留限制的最旧行 |
| `display_rows` | `fn(&self) -> Vec<Row>` | 获取用于显示的行快照（包含当前未完成行） |
| `take_prefix_by_width` | `fn(text, max_cols) -> (String, &str, usize)` | 按显示宽度截取文本前缀 |

## 依赖关系

- **引用的 crate**: `unicode_width`
- **被引用方**: `streaming.rs`（流式输出渲染）、`history_cell.rs`

## 实现备注

- 分片无关性：无论文本是一次性推入还是分多次推入，最终产生的行相同。
- 宽度变更时使用简单策略：收集所有文本后完全重新换行。
- 零宽字符被当作宽度 1 处理以避免无限循环。
- 包含单元测试验证 ASCII/emoji/CJK 字符的换行、分片无关性和宽度变更行为。
