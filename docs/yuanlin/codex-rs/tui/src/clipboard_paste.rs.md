# `clipboard_paste.rs` — 剪贴板图像粘贴与路径规范化

## 功能概述

本文件实现了从系统剪贴板获取图像并编码为 PNG 的功能，同时提供粘贴文本路径的规范化处理。它支持从剪贴板读取图像数据或文件列表（如 Finder 复制的文件），在 WSL 环境下可回退到 PowerShell 获取 Windows 剪贴板图像。路径规范化支持 `file://` URL、Windows/UNC 路径和 shell 转义路径。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PasteImageError` | 枚举 | 粘贴图像错误类型：ClipboardUnavailable / NoImage / EncodeFailed / IoError |
| `EncodedImageFormat` | 枚举 | 编码图像格式：Png / Jpeg / Other |
| `PastedImageInfo` | 结构体 | 粘贴的图像信息：宽度、高度、编码格式 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `paste_image_as_png` | `fn() -> Result<(Vec<u8>, PastedImageInfo), PasteImageError>` | 从剪贴板捕获图像并编码为 PNG 字节 |
| `paste_image_to_temp_png` | `fn() -> Result<(PathBuf, PastedImageInfo), PasteImageError>` | 将剪贴板图像保存为临时 PNG 文件 |
| `normalize_pasted_path` | `fn(pasted: &str) -> Option<PathBuf>` | 规范化粘贴的文本路径（支持 file:// URL、Windows 路径、shell 转义） |
| `pasted_image_format` | `fn(path: &Path) -> EncodedImageFormat` | 根据文件扩展名推断图像格式 |
| `is_probably_wsl` | `fn() -> bool`（仅 Linux） | 检测当前环境是否为 WSL |

## 依赖关系

- **引用的 crate**: `arboard`（剪贴板访问）、`image`（图像处理）、`tempfile`、`url`、`shlex`
- **被引用方**: `chatwidget.rs`（处理粘贴事件）、`clipboard_text.rs`（WSL 检测）

## 实现备注

- WSL 回退通过 `powershell.exe` 执行 PowerShell 脚本将 Windows 剪贴板图像保存为临时文件，然后将 Windows 路径转换为 WSL 路径（如 `C:\Users\...` → `/mnt/c/Users/...`）。
- Android/Termux 平台返回"不支持"错误。
- 路径规范化优先检测 Windows 路径（驱动器字母 + UNC），避免 POSIX shlex 将反斜杠当作转义字符。
- 包含大量单元测试覆盖各种路径格式和平台场景。
