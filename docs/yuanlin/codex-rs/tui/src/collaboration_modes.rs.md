# `collaboration_modes.rs` — 协作模式预设管理

## 功能概述

本文件提供协作模式（Collaboration Mode）预设的查询和循环切换功能。它从 `ModelCatalog` 获取可用的协作模式列表，过滤出 TUI 可见的模式（通过 `ModeKind::is_tui_visible` 判断），并提供默认模式选择、按类型查找和循环切换下一个模式的能力。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `presets_for_tui` | `fn(model_catalog) -> Vec<CollaborationModeMask>` | 返回 TUI 可见的协作模式预设列表 |
| `default_mask` | `fn(model_catalog) -> Option<CollaborationModeMask>` | 返回默认协作模式，优先选择 `ModeKind::Default` |
| `mask_for_kind` | `fn(model_catalog, kind) -> Option<CollaborationModeMask>` | 按 `ModeKind` 查找协作模式 |
| `next_mask` | `fn(model_catalog, current) -> Option<CollaborationModeMask>` | 循环切换到下一个协作模式预设 |
| `default_mode_mask` | `fn(model_catalog) -> Option<CollaborationModeMask>` | 获取 Default 模式 |
| `plan_mask` | `fn(model_catalog) -> Option<CollaborationModeMask>` | 获取 Plan 模式 |

## 依赖关系

- **引用的 crate**: `codex_protocol::config_types`（CollaborationModeMask、ModeKind）
- **被引用方**: `chatwidget.rs`、`app.rs`（模式切换和状态栏显示）
