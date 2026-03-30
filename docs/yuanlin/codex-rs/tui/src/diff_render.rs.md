# `diff_render.rs` — 统一差异（Unified Diff）渲染器

## 功能概述

本文件实现了带行号、沟槽标记和可选语法高亮的统一差异渲染器。每个 `FileChange` 变体（Add / Delete / Update）被渲染为一组差异行，每行包含右对齐的行号、沟槽符号（`+` / `-` / ` `）和内容文本。支持深色/浅色终端主题适配、truecolor/256色/16色调色板、语法主题作用域背景色覆盖、跨行的语法高亮状态保持、以及长行硬换行。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `DiffSummary` | 结构体 | 差异摘要，包含渲染行和统计信息 |
| `DiffTheme` | 结构体 | 差异主题，根据终端背景明暗选择颜色调色板 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_diff_summary` | `fn(changes, width) -> DiffSummary` | 从文件变更列表创建差异摘要 |
| `display_path_for` | `fn(path) -> String` | 生成显示用的文件路径（相对于 HOME 目录简化） |

## 依赖关系

- **引用的 crate**: `diffy`（差异计算）、`ratatui`（渲染类型）、`crate::color`（明暗判断）、`crate::render::highlight`（语法高亮）
- **被引用方**: `history_cell.rs`（文件变更单元渲染）、`app.rs`（`/diff` 命令）

## 实现备注

- Update 差异的高亮策略：每个 hunk 内作为连续块高亮以保持 syntect 解析器状态，跨 hunk 不保持状态。
- 语法主题作用域（`markup.inserted`/`markup.deleted`）覆盖硬编码调色板。
- ANSI-16 模式始终使用纯前景着色，忽略主题背景色。
- 长行在可用列宽处硬换行，保持样式跨分割点不丢失。
