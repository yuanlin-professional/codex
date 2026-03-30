# `resume_agent.rs` — V1 恢复已关闭子代理处理器

## 功能概述
该文件实现了 V1 多代理协作系统中的 `resume_agent` 工具处理器。它接收一个代理 ID，检查深度限制后尝试恢复已关闭的代理。如果目标代理状态为 `NotFound`，则通过 `resume_agent_from_rollout` 从 rollout 数据中恢复；否则直接返回当前状态。操作前后发射 `CollabResumeBeginEvent` / `CollabResumeEndEvent` 事件，并记录遥测计数。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Handler` | struct | `resume_agent` 工具处理器，实现 `ToolHandler` trait |
| `ResumeAgentResult` | struct | 返回结果，包含恢复后的 `status: AgentStatus` |
| `ResumeAgentArgs` | struct | 参数结构体，包含 `id: String` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<ResumeAgentResult, FunctionCallError>` | 检查深度限制，恢复或查询代理状态 |
| `try_resume_closed_agent` | `async fn(session, turn, receiver_thread_id, child_depth) -> Result<(), FunctionCallError>` | 构建恢复配置并调用 `resume_agent_from_rollout` 恢复已关闭代理 |

## 依赖关系
- **引用的父模块**: 通过 `use super::*` 引入，额外引用 `crate::agent::next_thread_spawn_depth`
- **被引用方**: `multi_agents.rs` 通过 `pub(crate) use resume_agent::Handler as ResumeAgentHandler` 导出

## 实现备注
- 恢复前检查子代理深度是否超过 `agent_max_depth` 限制
- 使用 `build_agent_resume_config` 构建恢复配置，该配置不含 base instructions（从 rollout/session metadata 获取）
- 通过 `try_resume_closed_agent` 封装恢复逻辑，失败时不中断事件发射流程
- 恢复成功后更新 `receiver_agent` 元数据以反映最新状态
