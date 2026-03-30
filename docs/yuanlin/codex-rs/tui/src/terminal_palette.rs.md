# `terminal_palette.rs` — 终端调色板检测和颜色适配

## 功能概述

检测终端的默认前景色/背景色，并根据终端的色彩能力（true-color/256色/16色）
选择最接近目标颜色的可显示色值。在 Unix 平台通过 crossterm 查询终端颜色，
测试和非 Unix 平台返回 None。维护版本号以便缓存失效。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `StdoutColorLevel` | enum | 终端色彩能力等级 |
| `DefaultColors` | struct | 默认前景/背景 RGB 颜色 |
| `Cache` | struct (imp) | 带惰性初始化的缓存容器 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `stdout_color_level` | `() -> StdoutColorLevel` | 检测终端色彩等级 |
| `best_color` | `(target) -> Color` | 选择终端可显示的最接近颜色 |
| `default_fg/default_bg` | `() -> Option<(u8,u8,u8)>` | 获取终端默认前景/背景色 |
| `requery_default_colors` | `()` | 重新查询终端颜色 |
| `palette_version` | `() -> u64` | 调色板版本号（用于缓存失效） |

## 依赖关系

- **引用的模块**: `crate::color`
- **引用的 crate**: `ratatui`, `supports_color`, `crossterm`（仅 Unix 非测试）

## 实现备注

- 包含完整的 256 色 Xterm 调色板查找表（`XTERM_COLORS`）。
- 使用感知距离（`perceptual_distance`）在 256 色模式下选择最接近的颜色。
- 跳过前 16 个系统颜色（因终端主题不同而变化），仅使用固定的 240 个颜色。
