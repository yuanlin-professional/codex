# `seek_sequence.rs` -- 模糊行序列匹配算法

## 功能概述

本文件实现了在文件行列表中查找目标行序列的算法。该算法用于 apply-patch 在应用 diff chunk 时定位需要替换的代码区域。它采用逐步放宽的匹配策略：先尝试精确匹配，然后去除尾部空白匹配，再去除首尾空白匹配，最后进行 Unicode 标点归一化匹配。当指定 `eof` 标志时优先从文件末尾开始搜索。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| （无导出结构体） | -- | 本文件仅导出一个函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `seek_sequence` | `fn(lines, pattern, start, eof) -> Option<usize>` | 在 lines 中从 start 位置查找 pattern 序列，返回起始索引 |
| `normalise` (内部) | `fn(&str) -> String` | 将 Unicode 特殊字符（各种 dash、引号、空格）归一化为 ASCII 等价字符 |

## 依赖关系

- **被引用方**: `lib.rs` 中的 `compute_replacements` 函数

## 实现备注

- 四级匹配策略：(1) 精确匹配 (2) trim_end 匹配 (3) trim 匹配 (4) Unicode 归一化匹配。
- Unicode 归一化覆盖：EN DASH、EM DASH、NON-BREAKING HYPHEN、MINUS SIGN 等 -> `-`；各种花式引号 -> `'` 或 `"`；各种特殊空格 -> 普通空格。
- 空 pattern 直接返回 `Some(start)`；pattern 长度超过 lines 长度时直接返回 `None`（修复了历史上的越界 panic）。
- `eof` 模式下从 `lines.len() - pattern.len()` 开始搜索，优先匹配文件末尾。
- 内嵌测试覆盖了精确匹配、尾部空白、首尾空白和 pattern 过长的场景。
