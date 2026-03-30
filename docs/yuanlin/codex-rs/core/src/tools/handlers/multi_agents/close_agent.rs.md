# `close_agent.rs` — V1 关闭子代理处理器

## 功能概述
该文件实现了 V1 多代理协作系统中的 `close_agent` 工具处理器。它接收一个代理 ID（`target`），订阅该代理的状态通道获取当前状态，然后向 `AgentControl` 发送关闭请求。在操作前后分别发射 `CollabCloseBeginEvent` 和 `CollabCloseEndEvent` 事件，最终返回代理关闭前的状态作为结果。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Handler` | struct | `close_agent` 工具处理器，实现 `ToolHandler` trait |
| `CloseAgentResult` | struct | 返回结果，包含 `previous_status: AgentStatus` |
| `CloseAgentArgs` | struct | 参数结构体，包含 `target: String` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<CloseAgentResult, FunctionCallError>` | 解析目标代理 ID，获取状态，发起关闭，返回先前状态 |
| `log_preview` | `fn log_preview(&self) -> String` | 将结果序列化为 JSON 文本用于日志预览 |
| `to_response_item` | `fn to_response_item(&self, ...) -> ResponseInputItem` | 将结果转换为模型可消费的响应项 |

## 依赖关系
- **引用的父模块**: 通过 `use super::*` 引入 `multi_agents.rs` 中的所有导入
- **被引用方**: `multi_agents.rs` 通过 `pub(crate) use close_agent::Handler as CloseAgentHandler` 导出

## 实现备注
- 即使状态订阅失败也会发射 `CollabCloseEndEvent`，确保事件配对完整
- 使用 `borrow_and_update` 获取最新状态快照
- 关闭操作的错误通过 `collab_agent_error` 映射为模型友好的错误消息
