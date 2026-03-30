# `proposed_plan.rs` — 计划块流解析器

## 功能概述

解析 `<proposed_plan>` 块标记，实现 `StreamTextParser` trait。将输入分解为 `ProposedPlanSegment` 序列：`Normal`（普通文本）、`ProposedPlanStart`（块开始）、`ProposedPlanDelta`（块内增量）、`ProposedPlanEnd`（块结束）。`visible_text` 仅包含 Normal 段。还提供 `strip_proposed_plan_blocks` 和 `extract_proposed_plan_text` 便捷函数。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ProposedPlanParser` | struct | 计划块解析器 |
| `ProposedPlanSegment` | enum | 计划段：Normal、Start、Delta、End |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `push_str` | `fn(&mut self, chunk) -> StreamTextChunk<ProposedPlanSegment>` | 推入文本块 |
| `finish` | `fn(&mut self) -> StreamTextChunk<ProposedPlanSegment>` | 刷新缓冲 |
| `strip_proposed_plan_blocks` | `fn(text) -> String` | 剥离计划块，返回可见文本 |
| `extract_proposed_plan_text` | `fn(text) -> Option<String>` | 提取最后一个计划块内容 |

## 依赖关系

- **引用的 crate**: 本 crate 内的 `TaggedLineParser`
- **被引用方**: `assistant_text.rs`

## 实现备注

- 标签必须独占一行（可有前导空白），行内的 `<proposed_plan>` 文本被视为普通文本。
- 未终止的块在 `finish` 时自动关闭。
