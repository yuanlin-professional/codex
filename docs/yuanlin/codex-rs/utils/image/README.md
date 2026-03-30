# image

## 功能概述

`codex-utils-image` 提供了图像加载、格式检测、缩放和编码功能，主要用于将图像处理为适合发送给 AI 模型的格式。支持 PNG、JPEG、GIF 和 WebP 格式，内置 LRU 缓存避免重复处理。

核心特性：
- 自动将超大图像缩放到 2048x768 以内
- 支持"原始模式"保留原始尺寸
- 基于 SHA-1 内容摘要的缓存键，避免文件内容变更时缓存失效
- 支持生成 data URL（Base64 编码）

## 目录结构

```
image/
├── Cargo.toml
└── src/
    ├── lib.rs          # 图像处理主逻辑与缓存
    └── error.rs        # ImageProcessingError 错误类型
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `base64` | Base64 编码（data URL 生成） |
| `image` | 图像解码、编码与缩放 |
| `codex-utils-cache` | LRU 缓存（内部依赖） |
| `mime_guess` | MIME 类型推测 |
| `thiserror` | 错误类型派生 |
| `tokio` | 异步文件 I/O |

## 核心接口/API

### 常量

```rust
pub const MAX_WIDTH: u32 = 2048;   // 缩放最大宽度
pub const MAX_HEIGHT: u32 = 768;   // 缩放最大高度
```

### `EncodedImage`

处理后的图像数据：

```rust
pub struct EncodedImage {
    pub bytes: Vec<u8>,    // 编码后的字节
    pub mime: String,       // MIME 类型（如 "image/png"）
    pub width: u32,
    pub height: u32,
}

impl EncodedImage {
    pub fn into_data_url(self) -> String;  // 转换为 data:image/...;base64,... URL
}
```

### `PromptImageMode`

图像处理模式：
- `ResizeToFit` -- 超出尺寸限制时缩放
- `Original` -- 保持原始尺寸

### `load_for_prompt_bytes(path, file_bytes, mode) -> Result<EncodedImage, ImageProcessingError>`

将图像字节处理为适合发送给模型的格式。自带 32 项容量的 LRU 缓存。

### `ImageProcessingError`

错误类型：`Read`（读取失败）、`Decode`（解码失败）、`Encode`（编码失败）、`UnsupportedImageFormat`（不支持的格式）。
