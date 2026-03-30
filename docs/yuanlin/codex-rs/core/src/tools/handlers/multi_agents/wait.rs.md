# `wait.rs` — V1 等待子代理完成处理器

## 功能概述
该文件实现了 V1 多代理协作系统中的 `wait_agent` 工具处理器。它接收一组代理 ID 目标和可选的超时时间，通过订阅代理状态变更通道并行等待任意一个代理达到终态（completed/error/not_found）。支持超时控制，超时值被钳制在 10 秒到 1 小时之间。返回已完成代理的状态映射和超时标志。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Handler` | struct | `wait_agent` 工具处理器，实现 `ToolHandler` trait |
| `WaitAgentResult` | struct | 返回结果，包含 `status: HashMap<String, AgentStatus>` 和 `timed_out: bool` |
| `WaitArgs` | struct | 参数结构体，包含 `targets: Vec<String>` 和 `timeout_ms: Option<i64>` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<WaitAgentResult, FunctionCallError>` | 订阅状态通道，并行等待终态或超时 |
| `wait_for_final_status` | `async fn(session, thread_id, status_rx) -> Option<(ThreadId, AgentStatus)>` | 监听单个代理的状态通道直到达到终态 |

## 依赖关系
- **引用的父模块**: 通过 `use super::*` 引入
- **额外引用**: `crate::agent::status::is_final`, `futures::FuturesUnordered`, `tokio::time::timeout_at`
- **被引用方**: `multi_agents.rs` 通过 `pub(crate) use wait::Handler as WaitAgentHandler` 导出

## 实现备注
- 使用 `FuturesUnordered` 并行监听多个代理的状态变更，任意一个达到终态即立即返回
- 首先检查初始状态，如果已有代理处于终态则直接返回，避免不必要的等待
- 在第一个终态结果返回后，通过 `now_or_never()` 非阻塞地收集其他已就绪的结果
- 超时被钳制到 `[MIN_WAIT_TIMEOUT_MS, MAX_WAIT_TIMEOUT_MS]` 范围内
- `target_by_thread_id` 映射用于将 `ThreadId` 转换回用户提供的目标标识（可能是 agent_path）
- 状态通道断开时回退到 `get_status` 查询最终状态
