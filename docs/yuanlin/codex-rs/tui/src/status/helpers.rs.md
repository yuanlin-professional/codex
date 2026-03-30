# `helpers.rs` — 状态显示辅助函数

## 功能概述

提供状态显示中通用的辅助函数，包括模型显示名称组合、账户显示信息组合、目录路径格式化、Token 数量紧凑格式化、计划类型显示名称等。这些函数被 `/status` 卡片和状态栏共同使用。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `compose_model_display` | `(model_name, entries) -> (String, Vec<String>)` | 组合模型显示名称和详情 |
| `compose_account_display` | `(account) -> Vec<Line>` | 组合账户显示信息 |
| `format_directory_display` | `(path, max_width) -> String` | 格式化目录路径（支持 ~ 缩写） |
| `format_tokens_compact` | `(tokens) -> String` | Token 数量紧凑格式化（如 1.2k、3.4m） |
| `plan_type_display_name` | `(plan_type) -> &str` | 计划类型的显示名称 |

## 依赖关系

- **引用的 crate**: `codex_core::config`, `codex_protocol::account`, `chrono`
- **被引用方**: `status::card`, `chatwidget::status_surfaces`
