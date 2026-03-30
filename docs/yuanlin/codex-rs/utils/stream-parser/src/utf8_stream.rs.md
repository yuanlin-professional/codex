# `utf8_stream.rs` — UTF-8 字节流封装

## 功能概述

`Utf8StreamParser<P>` 封装任意 `StreamTextParser`，接受原始字节输入，内部缓冲可能跨块边界的部分 UTF-8 码点。无效 UTF-8 会回滚整个推入的块以保持内层解析器状态干净。EOF 时如果仍有未完成的码点则返回 `IncompleteUtf8AtEof` 错误。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Utf8StreamParser<P>` | struct | UTF-8 字节流封装解析器 |
| `Utf8StreamParserError` | enum | 错误：`InvalidUtf8`、`IncompleteUtf8AtEof` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `push_bytes` | `fn(&mut self, chunk) -> Result<StreamTextChunk<...>>` | 推入原始字节 |
| `finish` | `fn(&mut self) -> Result<StreamTextChunk<...>>` | 刷新缓冲 |
| `into_inner` | `fn(self) -> Result<P>` | 提取内层解析器（需无缓冲字节） |
| `into_inner_lossy` | `fn(self) -> P` | 丢弃缓冲的部分码点，提取内层解析器 |

## 依赖关系

- **引用的 crate**: 无外部依赖
- **被引用方**: 需要从字节流解析文本的模块

## 实现备注

- 无效 UTF-8 块会被完全回滚（截断到推入前的长度），后续有效块可继续正常处理。
- 测试验证了跨块分割码点（é = 0xC3 0xA9）、无效字节回滚、EOF 不完整码点等场景。
