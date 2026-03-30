# `wait.rs` — V2 等待子代理完成处理器

## 功能概述
该文件实现了 V2 多代理协作系统中的 `wait_agent` 工具处理器。与 V1 版的核心区别在于：V2 使用 `resolve_agent_targets` 通过路径解析目标代理，返回结果简化为一条摘要消息（`"Wait completed."` 或 `"Wait timed out."`），不再返回每个代理的具体完成状态内容。等待逻辑与 V1 共享相同的 `FuturesUnordered` 并行监听模式。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Handler` | struct | V2 `wait_agent` 工具处理器，实现 `ToolHandler` trait |
| `WaitAgentResult` | struct | 返回结果，包含 `message: String` 和 `timed_out: bool` |
| `WaitArgs` | struct | 参数结构体，包含 `targets: Vec<String>` 和 `timeout_ms: Option<i64>` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<WaitAgentResult, FunctionCallError>` | 解析目标、订阅状态通道、并行等待终态或超时 |
| `wait_for_final_status` | `async fn(session, thread_id, status_rx) -> Option<(ThreadId, AgentStatus)>` | 监听单个代理状态直到终态 |
| `from_timed_out` | `fn(timed_out: bool) -> WaitAgentResult` | 根据超时标志构建结果摘要消息 |

## 依赖关系
- **引用的父模块**: 通过 `use super::*` 引入
- **额外引用**: `crate::agent::status::is_final`, `futures::FuturesUnordered`, `tokio::time::timeout_at`
- **被引用方**: `multi_agents_v2.rs` 通过 `pub(crate) use wait::Handler as WaitAgentHandler` 导出

## 实现备注
- V2 版的 `WaitAgentResult` 不包含 `status` HashMap，仅提供简单的完成/超时摘要
- 等待逻辑与 V1 版几乎相同：先检查初始终态、使用 `FuturesUnordered` 并行等待、超时钳制
- 使用 `resolve_agent_targets` 替代 V1 的 `parse_agent_id_targets`，支持路径寻址
- 事件模型（`CollabWaitingBeginEvent` / `CollabWaitingEndEvent`）与 V1 保持一致
