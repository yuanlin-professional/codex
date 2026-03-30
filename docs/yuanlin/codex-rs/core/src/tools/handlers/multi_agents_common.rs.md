# `multi_agents_common.rs` — 多代理公共工具函数

## 功能概述
该文件为 `multi_agents` 和 `multi_agents_v2` 两套多代理工具处理器提供公共的辅助函数和常量。主要包括：工具输出的序列化与格式化、代理状态构建、错误映射、子代理生成时的配置构建（含模型选择与 reasoning effort 验证）、以及协作输入的解析逻辑。它是多代理系统的基础设施层。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| - | 常量 `MIN_WAIT_TIMEOUT_MS` | 最小等待超时 10 秒，防止紧密轮询消耗 CPU |
| - | 常量 `DEFAULT_WAIT_TIMEOUT_MS` | 默认等待超时 30 秒 |
| - | 常量 `MAX_WAIT_TIMEOUT_MS` | 最大等待超时 1 小时 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `function_arguments` | `fn(payload: ToolPayload) -> Result<String, FunctionCallError>` | 从 `ToolPayload::Function` 中提取参数字符串 |
| `tool_output_json_text` | `fn<T: Serialize>(value, tool_name) -> String` | 将工具输出序列化为 JSON 文本 |
| `tool_output_response_item` | `fn(call_id, payload, value, success, tool_name) -> ResponseInputItem` | 构建模型可消费的响应输入项 |
| `tool_output_code_mode_result` | `fn<T: Serialize>(value, tool_name) -> JsonValue` | 构建 code mode 下的 JSON 结果 |
| `build_wait_agent_statuses` | `fn(statuses, receiver_agents) -> Vec<CollabAgentStatusEntry>` | 将代理状态映射构建为带昵称和角色的状态条目列表 |
| `collab_spawn_error` | `fn(err: CodexErr) -> FunctionCallError` | 将 `CodexErr` 映射为适合模型消费的生成错误 |
| `collab_agent_error` | `fn(agent_id, err: CodexErr) -> FunctionCallError` | 将代理级别的错误映射为模型可读的消息 |
| `thread_spawn_source` | `fn(...) -> Result<SessionSource, FunctionCallError>` | 构建子代理的 `SessionSource`，包含 `AgentPath` 层级 |
| `parse_collab_input` | `fn(message, items) -> Result<Op, FunctionCallError>` | 解析协作输入，确保 message 和 items 二选一且非空 |
| `build_agent_spawn_config` | `fn(base_instructions, turn) -> Result<Config, FunctionCallError>` | 基于父级 turn 上下文构建子代理的初始配置 |
| `build_agent_resume_config` | `fn(turn, child_depth) -> Result<Config, FunctionCallError>` | 构建恢复已关闭代理的配置（不含 base instructions） |
| `apply_spawn_agent_runtime_overrides` | `fn(config, turn) -> Result<(), FunctionCallError>` | 将运行时状态（审批策略、沙箱、cwd）从 turn 复制到子配置 |
| `apply_spawn_agent_overrides` | `fn(config, child_depth)` | 当子代理深度达到上限时禁用 SpawnCsv/Collab 特性 |
| `apply_requested_spawn_agent_model_overrides` | `async fn(session, turn, config, requested_model, reasoning_effort) -> Result<(), _>` | 验证并应用用户请求的模型和推理强度覆盖 |

## 依赖关系
- **引用的 crate**: `serde`, `serde_json`, `codex_features`, `codex_protocol`
- **引用的内部模块**: `crate::agent::AgentStatus`, `crate::codex::Session`, `crate::codex::TurnContext`, `crate::config::Config`, `crate::error::CodexErr`, `crate::models_manager`
- **被引用方**: `multi_agents.rs` 和 `multi_agents_v2.rs` 通过 `pub(crate) use crate::tools::handlers::multi_agents_common::*` 导入

## 实现备注
- `build_wait_agent_statuses` 对已知代理优先排列，未知代理按 thread_id 字典序追加
- `apply_requested_spawn_agent_model_overrides` 使用离线模型列表查找以避免网络请求
- `parse_collab_input` 强制执行 message/items 互斥约束，防止歧义输入
- 子代理深度超限时会自动禁用进一步生成能力，但 MultiAgentV2 不受此限制
