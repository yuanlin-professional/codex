# `close_agent.rs` — V2 关闭子代理处理器

## 功能概述
该文件实现了 V2 多代理协作系统中的 `close_agent` 工具处理器。与 V1 版不同，V2 版使用 `resolve_agent_target` 通过路径或名称解析目标代理，并增加了对根代理的保护（禁止关闭根代理）。其余流程与 V1 版类似：获取状态、发起关闭、发射事件、返回先前状态。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Handler` | struct | V2 `close_agent` 工具处理器，实现 `ToolHandler` trait |
| `CloseAgentResult` | struct | 返回结果，包含 `previous_status: AgentStatus` |
| `CloseAgentArgs` | struct | 参数结构体，包含 `target: String` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<CloseAgentResult, FunctionCallError>` | 解析目标、验证非根、关闭代理并返回先前状态 |

## 依赖关系
- **引用的父模块**: 通过 `use super::*` 引入
- **额外引用**: `codex_protocol::AgentPath` 用于根代理检测
- **被引用方**: `multi_agents_v2.rs` 通过 `pub(crate) use close_agent::Handler as CloseAgentHandler` 导出

## 实现备注
- 增加了 `agent_path.is_root()` 检查，拒绝关闭根代理以防止系统崩溃
- 使用 `resolve_agent_target` 支持路径寻址，而非 V1 的直接 ThreadId 解析
- 事件发射和错误处理模式与 V1 版保持一致
