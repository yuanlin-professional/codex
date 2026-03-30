# `user_input.rs` — 用户输入类型定义

## 功能概述

定义了用户向 Codex 发送消息时的输入类型体系。`UserInput` 是一个标签化枚举，支持文本、远程图片 URL、本地图片路径、技能(Skill)引用和提及(Mention)引用等多种输入形式。同时定义了文本元素(`TextElement`)和字节范围(`ByteRange`)类型，用于在文本中标记特殊元素（如图片占位符），支持跨历史和恢复会话时保持富文本标记的一致性。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UserInput` | 枚举 | 用户输入类型（Text / Image / LocalImage / Skill / Mention） |
| `TextElement` | 结构体 | 文本中的特殊元素标记（字节范围 + 可选占位符） |
| `ByteRange` | 结构体 | UTF-8 文本缓冲区中的字节范围（start 包含，end 不包含） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `TextElement::new` | `fn new(byte_range, placeholder) -> Self` | 创建新的文本元素 |
| `TextElement::map_range` | `fn map_range<F>(&self, map: F) -> Self` | 用映射函数重新映射字节范围 |
| `TextElement::placeholder` | `fn placeholder<'a>(&'a self, text: &'a str) -> Option<&'a str>` | 获取占位符文本，回退到文本缓冲区切片 |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `ts_rs`
- **被引用方**: `items.rs` 中的 `UserMessageItem`、`protocol.rs` 中的事件

## 实现备注

- `UserInput` 使用 `#[non_exhaustive]` 标记，允许未来添加新变体而不破坏 API 兼容性。
- `MAX_USER_INPUT_TEXT_CHARS` 常量限制单条用户消息最大字符数（1MB），防止占用过多上下文窗口。
- `ByteRange` 实现了 `From<std::ops::Range<usize>>` 便于构造。
