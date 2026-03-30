# `error.rs` — 图像处理错误类型

## 功能概述

定义 `ImageProcessingError` 枚举，包含图像读取（`Read`）、解码（`Decode`）、编码（`Encode`）和不支持格式（`UnsupportedImageFormat`）四种变体。提供 `decode_error` 工厂方法根据解码结果生成合适的错误变体，以及 `is_invalid_image` 方法判断是否为无效图像数据。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ImageProcessingError` | enum | 图像处理错误：Read、Decode、Encode、UnsupportedImageFormat |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `decode_error` | `fn(path, source) -> Self` | 根据解码错误类型构造适当错误 |
| `is_invalid_image` | `fn(&self) -> bool` | 判断是否为无效图像解码错误 |

## 依赖关系

- **引用的 crate**: `image`, `mime_guess`, `thiserror`
- **被引用方**: `image/src/lib.rs`
