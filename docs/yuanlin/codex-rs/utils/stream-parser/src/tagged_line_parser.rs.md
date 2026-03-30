# `tagged_line_parser.rs` — 基于行的标签块解析器

## 功能概述

`TaggedLineParser<T>` 是内部使用的行级标签块解析器。逐字符处理输入，在每行开头检测标签，标签必须独占一行（可有前导/尾随空白）。输出为 `TaggedLineSegment` 序列：`Normal`、`TagStart`、`TagDelta`、`TagEnd`。相邻的同类型段会被合并。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TaggedLineParser<T>` | struct | 行级标签解析器 |
| `TagSpec<T>` | struct | 标签规范：open/close 字符串和 tag 标识 |
| `TaggedLineSegment<T>` | enum | 解析段：Normal、TagStart、TagDelta、TagEnd |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse` | `fn(&mut self, delta) -> Vec<TaggedLineSegment<T>>` | 解析增量文本 |
| `finish` | `fn(&mut self) -> Vec<TaggedLineSegment<T>>` | 刷新缓冲，关闭未终止块 |

## 依赖关系

- **引用的 crate**: 无
- **被引用方**: `proposed_plan.rs`

## 实现备注

- `detect_tag` 标志在每行开头为 `true`，一旦确认非标签行则切换为 `false`。
- 行内有额外文本的标签行被视为普通文本。
- 测试验证了跨块缓冲和带额外文本的标签行拒绝。
