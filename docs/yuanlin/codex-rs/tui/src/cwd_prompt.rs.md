# `cwd_prompt.rs` — 工作目录选择提示界面

## 功能概述

本文件实现了恢复/分叉会话时的工作目录选择提示。当会话记录的 CWD 与当前工作目录不同时，显示一个选择界面让用户选择使用"会话目录"还是"当前目录"。界面支持键盘导航（上下箭头、j/k、数字快捷键、Enter 确认、Esc 默认选择会话目录、Ctrl+C/D 退出）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CwdPromptAction` | 枚举 | 操作类型：Resume / Fork |
| `CwdSelection` | 枚举 | 选择结果：Current / Session |
| `CwdPromptOutcome` | 枚举 | 提示结果：Selection(CwdSelection) / Exit |
| `CwdPromptScreen` | 结构体 | 提示界面状态，持有选项、高亮状态和选择结果 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_cwd_selection_prompt` | `async fn(tui, action, current_cwd, session_cwd) -> Result<CwdPromptOutcome>` | 运行 CWD 选择提示的完整事件循环 |

## 依赖关系

- **引用的 crate**: `ratatui`（渲染）、`crossterm`（键盘事件）、`tokio_stream`
- **被引用方**: `lib.rs` 中的 `resolve_cwd_for_resume_or_fork`

## 实现备注

包含快照测试（insta）验证渲染输出，以及行为测试验证键盘操作的正确性。
