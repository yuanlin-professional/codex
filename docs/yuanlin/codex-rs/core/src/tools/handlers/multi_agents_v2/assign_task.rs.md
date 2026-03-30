# `assign_task.rs` — V2 分配任务处理器

## 功能概述
该文件实现了 V2 多代理协作系统中的 `assign_task` 工具处理器。它是 `send_message` 的对应物，区别在于 `assign_task` 使用 `MessageDeliveryMode::TriggerTurn` 模式，会立即唤醒目标代理开始新的 turn。实际逻辑委托给 `message_tool::handle_message_tool` 共享实现。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Handler` | struct | `assign_task` 工具处理器，实现 `ToolHandler` trait，输出类型为 `MessageToolResult` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<MessageToolResult, FunctionCallError>` | 以 `TriggerTurn` 模式调用 `handle_message_tool` |

## 依赖关系
- **引用的父模块**: 通过 `use super::*` 引入
- **直接依赖**: `message_tool::MessageDeliveryMode`, `message_tool::MessageToolResult`, `message_tool::handle_message_tool`
- **被引用方**: `multi_agents_v2.rs` 通过 `pub(crate) use assign_task::Handler as AssignTaskHandler` 导出

## 实现备注
- `assign_task` 和 `send_message` 共享完全相同的参数结构和消息投递逻辑，唯一区别是 `trigger_turn` 标志
- `TriggerTurn` 模式设置 `InterAgentCommunication.trigger_turn = true`，使目标代理立即开始处理
