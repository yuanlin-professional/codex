# `spawn.rs` — V1 生成子代理处理器

## 功能概述
该文件实现了 V1 多代理协作系统中的 `spawn_agent` 工具处理器。它接收初始消息/items、可选的角色类型（`agent_type`）、模型选择、推理强度等参数，在检查深度限制后构建子代理配置，应用角色和运行时覆盖，最终通过 `AgentControl` 生成新代理。返回新代理的 `agent_id` 和可选的 `nickname`。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Handler` | struct | `spawn_agent` 工具处理器，实现 `ToolHandler` trait |
| `SpawnAgentResult` | struct | 返回结果，包含 `agent_id: String` 和可选 `nickname` |
| `SpawnAgentArgs` | struct | 参数结构体，包含 `message`、`items`、`agent_type`、`model`、`reasoning_effort`、`fork_context` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<SpawnAgentResult, FunctionCallError>` | 构建配置、应用覆盖、生成子代理并返回 ID |

## 依赖关系
- **引用的父模块**: 通过 `use super::*` 引入
- **额外引用**: `crate::agent::control::SpawnAgentOptions`, `crate::agent::role::*`, `crate::agent::exceeds_thread_spawn_depth_limit`, `crate::agent::next_thread_spawn_depth`
- **被引用方**: `multi_agents.rs` 通过 `pub(crate) use spawn::Handler as SpawnAgentHandler` 导出

## 实现备注
- 配置构建流程：`build_agent_spawn_config` -> `apply_requested_spawn_agent_model_overrides` -> `apply_role_to_config` -> `apply_spawn_agent_runtime_overrides` -> `apply_spawn_agent_overrides`
- `fork_context` 标志为 true 时会将当前 `call_id` 传递给子代理作为 fork 上下文
- 生成后从 `agent_config_snapshot` 获取生效的模型和推理强度信息，回填到 `CollabAgentSpawnEndEvent`
- V1 版的 `SpawnAgentResult` 返回 `agent_id`（ThreadId 字符串），V2 版则返回 `task_name`
- 遥测计数器 `codex.multi_agent.spawn` 记录按角色分类的生成次数
