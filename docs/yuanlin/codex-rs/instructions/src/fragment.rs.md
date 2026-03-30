# `fragment.rs` — 上下文用户片段定义

## 功能概述

定义 Codex 提示中的上下文用户片段标记。提供 `ContextualUserFragmentDefinition` 结构体用于标识和包装 AGENTS.md 指令和技能指令片段。支持通过开始/结束标记进行文本匹配、包装和转换为 ResponseItem。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ContextualUserFragmentDefinition` | struct | 片段定义：start_marker + end_marker |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `matches_text` | `(&self, text) -> bool` | 检查文本是否匹配此片段格式 |
| `wrap` | `(&self, body) -> String` | 用标记包装内容 |
| `into_message` | `(self, text) -> ResponseItem` | 转换为用户角色的 ResponseItem |

## 依赖关系

- **被引用方**: `instructions/src/user_instructions.rs`、core 提示构建模块
