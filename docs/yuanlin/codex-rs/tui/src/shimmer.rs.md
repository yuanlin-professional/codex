# `shimmer.rs` — 文本闪烁动画效果

## 功能概述

实现基于时间的文本闪烁（shimmer）动画效果，用于 TUI 中的加载指示器。
使用正弦波函数在文本上产生移动的高亮光带，支持 true-color 和低色彩模式的回退。
动画以进程启动时间为基准，周期为 2 秒。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `shimmer_spans` | `(text) -> Vec<Span>` | 生成带闪烁样式的 Span 序列 |

## 依赖关系

- **引用的模块**: `crate::color`, `crate::terminal_palette`
- **引用的 crate**: `ratatui`, `supports_color`

## 实现备注

- true-color 模式下使用 `blend` 函数在前景色和背景色之间插值。
- 低色彩模式回退到 DIM/normal/BOLD 三级明暗。
- 光带半宽为 5 个字符，使用余弦函数产生平滑过渡。
