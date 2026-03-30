# `review_format.rs` — 代码审查结果格式化输出

## 功能概述

此文件提供代码审查发现（findings）的纯文本格式化功能，将审查结果渲染为人类可读的文本，支持带勾选框（用于交互式选择）和无勾选框两种模式。它是 UI 无关的格式化层，输出纯字符串供上层（如 TUI）进一步添加样式。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| （无独立类型定义） | — | — |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `format_review_findings_block` | `(findings, selection) -> String` | 格式化审查发现列表，支持可选的勾选框标记 |
| `render_review_output_text` | `(output: &ReviewOutputEvent) -> String` | 渲染完整的审查输出文本，包含解释和发现列表 |

## 依赖关系

- **引用的 crate**: `crate::protocol`（`ReviewFinding`、`ReviewOutputEvent`）
- **被引用方**: `lib.rs` 公开导出此模块，TUI 审查界面使用

## 实现备注

- 有勾选框时格式为 `- [x] Title — path:start-end` 或 `- [ ] Title — path:start-end`
- 无勾选框时格式为 `- Title — path:start-end`
- 缺失索引默认为已选中（`unwrap_or(true)`）
- 当解释和发现都为空时返回 fallback 消息 `"Reviewer failed to output a response."`
