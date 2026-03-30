# `lib.rs` — 图像加载、缩放与编码

## 功能概述

提供图像加载和处理功能，主要用于准备发送到 API 的提示图像。核心函数 `load_for_prompt_bytes` 接受文件字节和处理模式，在 `ResizeToFit` 模式下将超过 2048x768 的图像缩小，在 `Original` 模式下保持原样。使用基于 SHA-1 内容摘要的 LRU 缓存避免重复处理。支持 PNG、JPEG、WebP 格式的直通和编码转换。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `EncodedImage` | struct | 编码后的图像：bytes、mime、width、height |
| `PromptImageMode` | enum | 处理模式：`ResizeToFit`、`Original` |
| `ImageCacheKey` | struct(private) | 缓存键：SHA-1 摘要 + 处理模式 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_for_prompt_bytes` | `fn(path, file_bytes, mode) -> Result<EncodedImage, ImageProcessingError>` | 加载并处理图像 |
| `into_data_url` | `fn(self) -> String` | 将编码图像转为 data URL |

## 依赖关系

- **引用的 crate**: `base64`, `codex_utils_cache`, `image`
- **被引用方**: 提示构造模块（需要嵌入图像的地方）

## 实现备注

- 缓存容量固定为 32 个条目。
- 支持 PNG、JPEG、WebP 格式保留原始字节；GIF 仅支持非动画且需转码为 PNG。
- JPEG 编码质量为 85，WebP 使用无损编码。
