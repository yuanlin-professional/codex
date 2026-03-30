# `spawn.rs` — V2 生成子代理处理器

## 功能概述
该文件实现了 V2 多代理协作系统中的 `spawn_agent` 工具处理器。与 V1 版的主要区别在于：V2 要求提供 `task_name` 作为代理的路径标识，返回 `task_name`（而非 `agent_id`）作为代理寻址方式；初始输入如果为纯文本会被包装为 `InterAgentCommunication` 消息格式，包含发送方和接收方的 `AgentPath`。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Handler` | struct | V2 `spawn_agent` 工具处理器，实现 `ToolHandler` trait |
| `SpawnAgentResult` | struct | 返回结果，包含 `agent_id: Option<String>`、`task_name: String`、`nickname: Option<String>` |
| `SpawnAgentArgs` | struct | 参数结构体，包含 `message`、`items`、`task_name`（必填）、`agent_type`、`model`、`reasoning_effort`、`fork_context` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<SpawnAgentResult, FunctionCallError>` | 构建配置、包装初始通信、生成子代理并返回 task_name |

## 依赖关系
- **引用的父模块**: 通过 `use super::*` 引入
- **额外引用**: `crate::agent::control::SpawnAgentOptions`, `crate::agent::role::*`, `codex_protocol::AgentPath`, `codex_protocol::protocol::InterAgentCommunication`
- **被引用方**: `multi_agents_v2.rs` 通过 `pub(crate) use spawn::Handler as SpawnAgentHandler` 导出

## 实现备注
- `task_name` 为必填字段，缺失时会在 `thread_spawn_source` 中构建 `AgentPath`
- 纯文本初始操作会被自动升级为 `Op::InterAgentCommunication`，携带发送方/接收方路径
- 非纯文本初始操作（如包含图片）保持原始 `Op::UserInput` 格式
- 生成的 `agent_id` 在 V2 结果中为 `None`，代理通过 `task_name` 路径寻址
- 配置构建流程与 V1 一致：`build_agent_spawn_config` -> 模型覆盖 -> 角色配置 -> 运行时覆盖 -> 深度覆盖
