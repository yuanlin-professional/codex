# `message_tool.rs` — V2 消息投递共享逻辑

## 功能概述
该文件为 V2 多代理系统中 `send_message` 和 `assign_task` 两个工具提供共享的参数解析和消息投递实现。两者使用完全相同的输入格式，仅在投递模式上有区别：`send_message` 使用 `QueueOnly` 模式（仅排队不唤醒），`assign_task` 使用 `TriggerTurn` 模式（排队并立即唤醒）。消息内容当前仅支持纯文本。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `MessageDeliveryMode` | enum | 消息投递模式：`QueueOnly`（仅排队）和 `TriggerTurn`（触发 turn） |
| `MessageToolArgs` | struct | 共享参数，包含 `target`、`items: Vec<UserInput>`、`interrupt` 标志 |
| `MessageToolResult` | struct | 共享返回结果，包含 `submission_id: String` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `unsupported_items_error` | `fn(self) -> &'static str` | 根据模式返回非文本输入的错误消息 |
| `apply` | `fn(self, communication) -> InterAgentCommunication` | 将投递模式应用到通信对象的 `trigger_turn` 字段 |
| `text_content` | `fn(items, mode) -> Result<String, FunctionCallError>` | 验证输入全部为文本类型并生成预览 |
| `handle_message_tool` | `async fn(invocation, mode) -> Result<MessageToolResult, FunctionCallError>` | 共享的消息投递主逻辑：解析目标、验证内容、可选中断、发送通信 |

## 依赖关系
- **引用的父模块**: 通过 `use super::*` 引入
- **额外引用**: `crate::agent::control::render_input_preview`, `codex_protocol::protocol::InterAgentCommunication`
- **被引用方**: `assign_task.rs` 和 `send_message.rs` 调用 `handle_message_tool`

## 实现备注
- 当前仅支持纯文本 `UserInput::Text` 内容，结构化内容（图片等）会被拒绝
- 使用 `InterAgentCommunication` 消息格式，包含发送方和接收方的 `AgentPath`
- `interrupt` 标志会在发送消息前先中断目标代理的当前操作
- 接收方的 `agent_path` 从元数据中获取，缺失时返回错误
