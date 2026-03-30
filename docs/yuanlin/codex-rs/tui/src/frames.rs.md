# `frames.rs` — ASCII 动画帧数据定义

## 功能概述

本文件在编译时嵌入多组 ASCII 艺术动画帧数据。每个变体包含 36 帧文本，通过 `include_str!` 宏从 `frames/` 目录加载。定义了 10 个动画变体（default、codex、openai、blocks、dots、hash、hbars、vbars、shapes、slug）和默认帧间隔（80ms）。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ALL_VARIANTS` | 常量 | 包含所有 10 个动画变体的静态切片 |
| `FRAME_TICK_DEFAULT` | 常量 | 默认帧间隔 80ms |

## 依赖关系

- **引用的 crate**: 无外部依赖（仅使用 `include_str!` 宏）
- **被引用方**: `ascii_animation.rs`（AsciiAnimation 使用这些帧数据驱动动画）
