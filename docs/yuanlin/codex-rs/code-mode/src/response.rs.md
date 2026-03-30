# `response.rs` -- Code Mode 响应内容类型定义

## 功能概述

定义了 Code Mode 中函数调用输出的内容项类型。`FunctionCallOutputContentItem` 枚举使用 serde 标签联合序列化，支持两种变体：`InputText`（纯文本输出）和 `InputImage`（图片 URL 输出，可选图像细节级别）。`ImageDetail` 枚举定义了图像细节级别：Auto、Low、High、Original。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ImageDetail` | enum | 图像细节级别：Auto / Low / High / Original |
| `FunctionCallOutputContentItem` | enum (tagged) | 函数调用输出项：`InputText { text }` 或 `InputImage { image_url, detail? }` |

## 依赖关系

- **引用的 crate**: `serde`
- **被引用方**: `runtime/value.rs`, `runtime/mod.rs`, `lib.rs`（公开导出）
