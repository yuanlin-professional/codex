# `text_encoding.rs` — Shell 输出文本编码检测与转换

## 功能概述

此文件提供 shell 输出的文本编码检测与转换工具。Windows 用户在 VS Code 中运行命令时经常遇到 CP1251 或 CP866 等编码页，导致无效 UTF-8。文件利用 `chardetng` 和 `encoding_rs` 自动检测和解码遗留编码，并特殊处理 IBM866 与 Windows-1252 在 0x80-0x9F 字节范围的冲突问题。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `WINDOWS_1252_PUNCT_BYTES` | 常量数组 | Windows-1252 智能标点的字节值列表（弯引号、破折号等） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `bytes_to_string_smart` | `(bytes: &[u8]) -> String` | 核心公共函数：尽力将字节转换为 UTF-8 字符串 |
| `detect_encoding` | `(bytes: &[u8]) -> &'static Encoding` | 检测字节序列的编码，含 IBM866/Windows-1252 冲突修正 |
| `decode_bytes` | `(bytes, encoding) -> String` | 使用指定编码解码字节 |
| `looks_like_windows_1252_punctuation` | `(bytes: &[u8]) -> bool` | 判断字节是否看起来像 Windows-1252 智能标点 |

## 依赖关系

- **引用的 crate**: `chardetng`（编码检测）、`encoding_rs`（编码转换）
- **被引用方**: `lib.rs` 公开导出 `bytes_to_string_smart`

## 实现备注

- 处理流程：先尝试 UTF-8 -> 检测编码 -> 用检测到的编码解码 -> 解码失败则 lossy UTF-8
- IBM866 与 Windows-1252 冲突：0x80-0x9F 范围在 IBM866 中是西里尔字母，在 Windows-1252 中是弯引号/破折号
- 修正条件：检测为 IBM866 + 高字节仅含标点值 + 有 ASCII 字母 -> 切换到 Windows-1252
- 不对 0xA0 以上字节的 IBM866 做修正，以保留合法的西里尔文本
