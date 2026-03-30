# `mod.rs` — 上下文管理器模块入口

## 功能概述

`context_manager` 模块的入口文件，负责组织和重导出上下文管理相关的子模块。上下文管理器用于追踪对话历史、token 用量估计和响应项的规范化处理。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ContextManager` | 结构体（重导出） | 对话历史上下文管理器，管理 token 用量和历史记录 |
| `TotalTokenUsageBreakdown` | 结构体（重导出） | token 总使用量分项统计 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `estimate_response_item_model_visible_bytes` | 重导出 | 估算响应项中模型可见的字节数 |
| `is_codex_generated_item` | 重导出 | 判断某个响应项是否由 Codex 自身生成 |
| `is_user_turn_boundary` | 重导出 | 判断某个项是否为用户轮次边界 |

## 依赖关系

- **引用的 crate**: 无直接外部依赖（通过子模块 `history`、`normalize`、`updates` 引用）
- **被引用方**: `codex-core` 的 session/turn 管理逻辑
