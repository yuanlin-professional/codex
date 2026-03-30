# `review_prompts.rs` — 代码审查提示词模板与解析

## 功能概述

此文件负责根据不同的代码审查目标（未提交变更、基础分支、特定提交、自定义）生成对应的审查提示词（prompt）。它使用模板引擎渲染变量化的提示词字符串，并提供用户友好的审查目标描述。对于基础分支审查，还会尝试计算 merge-base 以提供精确的 diff 基准。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ResolvedReviewRequest` | 结构体 | 解析后的审查请求，包含目标、提示词和用户提示 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `resolve_review_request` | `(request, cwd) -> anyhow::Result<ResolvedReviewRequest>` | 解析审查请求，生成提示词和用户提示 |
| `review_prompt` | `(target, cwd) -> anyhow::Result<String>` | 根据审查目标生成提示词字符串 |
| `user_facing_hint` | `(target) -> String` | 生成用户可见的审查目标描述 |
| `render_review_prompt` | `(template, variables) -> String` | 使用模板引擎渲染提示词 |

## 依赖关系

- **引用的 crate**: `codex_git_utils`（`merge_base_with_head`）、`codex_protocol`（`ReviewRequest`、`ReviewTarget`）、`codex_utils_template`
- **被引用方**: `lib.rs` 公开导出此模块，审查功能流程使用

## 实现备注

- 基础分支审查有两套模板：主模板（使用 merge-base SHA）和备用模板（让模型自行查找 merge-base）
- 模板使用 `LazyLock` 延迟初始化，解析失败时 panic
- 提交审查支持可选的标题字段
- 自定义审查指令不能为空
- `ResolvedReviewRequest` 可以反向转换为 `ReviewRequest`
