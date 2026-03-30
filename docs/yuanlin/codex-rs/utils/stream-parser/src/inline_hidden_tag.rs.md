# `inline_hidden_tag.rs` — 通用内联隐藏标签流解析器

## 功能概述

`InlineHiddenTagParser<T>` 是通用的流式解析器，可配置一个或多个内联标签规范（`InlineTagSpec`）来隐藏匹配的标签并提取其内容。支持跨块边界的部分标签匹配，使用后缀-前缀长度计算来决定安全输出的文本量。匹配为字面量、非嵌套。EOF 时自动关闭未终止的标签。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `InlineHiddenTagParser<T>` | struct | 通用内联隐藏标签解析器 |
| `InlineTagSpec<T>` | struct | 标签规范：tag 标识、open/close 分隔符 |
| `ExtractedInlineTag<T>` | struct | 提取结果：tag 标识和内容字符串 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(specs: Vec<InlineTagSpec<T>>) -> Self` | 创建解析器 |
| `push_str` | `fn(&mut self, chunk) -> StreamTextChunk<ExtractedInlineTag<T>>` | 推入文本块 |
| `finish` | `fn(&mut self) -> StreamTextChunk<ExtractedInlineTag<T>>` | 刷新缓冲 |

## 依赖关系

- **引用的 crate**: 无外部依赖
- **被引用方**: `citation.rs`

## 实现备注

- 多标签同位置时优先选择最长 open 分隔符。
- `longest_suffix_prefix_len` 用于检测可能跨块的部分标签前缀。
- 测试验证了多标签类型、非 ASCII 分隔符和最长匹配优先。
