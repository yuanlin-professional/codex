# `citation.rs` — 引用标签流解析器

## 功能概述

`CitationStreamParser` 是对 `InlineHiddenTagParser` 的便捷封装，专门处理 `<oai-mem-citation>...</oai-mem-citation>` 标签。剥离标签后返回纯文本，提取引用内容为字符串列表。匹配为字面量非嵌套，EOF 时自动关闭未终止的标签。还提供 `strip_citations` 便捷函数用于一次性处理完整字符串。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CitationStreamParser` | struct | 引用标签流解析器 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `push_str` | `fn(&mut self, chunk) -> StreamTextChunk<String>` | 推入文本块 |
| `finish` | `fn(&mut self) -> StreamTextChunk<String>` | 刷新缓冲 |
| `strip_citations` | `fn(text) -> (String, Vec<String>)` | 一次性剥离引用 |

## 依赖关系

- **引用的 crate**: 本 crate 内的 `InlineHiddenTagParser`
- **被引用方**: `assistant_text.rs`, 消息处理模块

## 实现备注

- 不支持嵌套标签（首个 close 标签匹配首个 open 标签）。
- 测试覆盖了跨块边界、部分标签缓冲、EOF 自动关闭和非嵌套行为。
