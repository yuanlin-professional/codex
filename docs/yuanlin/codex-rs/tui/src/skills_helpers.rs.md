# `skills_helpers.rs` — 技能显示和匹配辅助函数

## 功能概述

提供技能（skill）在 TUI 中的显示名称获取、描述获取、名称截断和模糊匹配等辅助函数。
被技能选择器和 mention 自动完成等组件使用。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `skill_display_name` | `(skill) -> &str` | 获取技能的显示名称 |
| `skill_description` | `(skill) -> &str` | 获取技能描述 |
| `truncate_skill_name` | `(name) -> String` | 截断技能名称到 21 字符 |
| `match_skill` | `(filter, display, name) -> Option<(indices, score)>` | 模糊匹配技能 |

## 依赖关系

- **引用的 crate**: `codex_core::skills`, `codex_utils_fuzzy_match`
- **引用的模块**: `crate::text_formatting`
