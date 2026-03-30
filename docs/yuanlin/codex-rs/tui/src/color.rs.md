# `color.rs` — 颜色工具函数

## 功能概述

本文件提供基础的颜色计算工具函数：判断背景色明暗（用于主题适配）、Alpha 混合两个 RGB 颜色、以及基于 CIE76 公式计算两个 RGB 颜色的感知距离。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `is_light` | `fn(bg: (u8, u8, u8)) -> bool` | 使用 ITU-R BT.601 亮度公式判断背景色是否为浅色（Y > 128） |
| `blend` | `fn(fg, bg, alpha) -> (u8, u8, u8)` | 使用线性插值将前景色与背景色进行 Alpha 混合 |
| `perceptual_distance` | `fn(a, b) -> f32` | 计算两个 RGB 颜色的 CIE76 感知距离（通过 sRGB → XYZ → Lab 转换） |

## 依赖关系

- **引用的 crate**: 无外部依赖
- **被引用方**: `diff_render.rs`（判断终端背景明暗以选择差异颜色主题）、`terminal_palette.rs`
