# `send_input.rs` — V1 向子代理发送输入处理器

## 功能概述
该文件实现了 V1 多代理协作系统中的 `send_input` 工具处理器。它向指定的子代理发送用户输入（文本消息或结构化 items），支持可选的中断（interrupt）功能以在发送前中断忙碌的代理。操作前后发射 `CollabAgentInteractionBeginEvent` / `CollabAgentInteractionEndEvent` 事件。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Handler` | struct | `send_input` 工具处理器，实现 `ToolHandler` trait |
| `SendInputResult` | struct | 返回结果，包含 `submission_id: String` |
| `SendInputArgs` | struct | 参数结构体，包含 `target`、可选 `message`/`items`、及 `interrupt` 标志 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<SendInputResult, FunctionCallError>` | 解析参数，可选中断目标代理，发送输入并返回提交 ID |

## 依赖关系
- **引用的父模块**: 通过 `use super::*` 引入，额外引用 `crate::agent::control::render_input_preview`
- **被引用方**: `multi_agents.rs` 通过 `pub(crate) use send_input::Handler as SendInputHandler` 导出

## 实现备注
- 使用 `parse_collab_input` 解析消息，确保 message 和 items 互斥
- `interrupt` 标志为 true 时会在发送输入前先调用 `interrupt_agent` 中断目标代理
- `render_input_preview` 生成输入内容的文本预览用于事件记录
- 返回的 `submission_id` 可用于后续追踪输入的处理状态
