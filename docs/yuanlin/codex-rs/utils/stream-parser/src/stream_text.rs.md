# `stream_text.rs` — 流文本核心类型与 trait

## 功能概述

定义流式文本解析的核心抽象。`StreamTextChunk<T>` 包含可立即渲染的 `visible_text` 和提取的隐藏载荷列表 `extracted`。`StreamTextParser` trait 定义了 `push_str`（推入新块）和 `finish`（刷新缓冲）两个方法。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `StreamTextChunk<T>` | struct | 解析结果：visible_text + extracted 列表 |
| `StreamTextParser` | trait | 流文本解析器 trait |

## 依赖关系

- **引用的 crate**: 无
- **被引用方**: 所有流解析器实现
