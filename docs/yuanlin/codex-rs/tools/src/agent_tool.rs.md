# `agent_tool.rs` -- Agent 管理工具定义

## 功能概述
定义完整的 Agent 生命周期管理工具集，包括 v1 和 v2 两代 API：spawn_agent（创建子 agent）、wait_agent（等待完成/超时）、send_message/send_input（发送消息）、close_agent（关闭）、list_agents（列出运行中的 agent）、resume_agent（恢复暂停的 agent）以及 assign_task（分配任务给已有 agent）。支持可选的模型选择、工具权限和工作目录配置。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SpawnAgentToolOptions` | struct | spawn 工具选项，包含可用模型列表和 agent 类型描述 |
| `WaitAgentTimeoutOptions` | struct | wait 工具超时选项：默认/最小/最大超时毫秒数 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_spawn_agent_tool_v1` | `fn(options) -> ToolSpec` | 创建 v1 版 spawn agent 工具 |
| `create_spawn_agent_tool_v2` | `fn(options) -> ToolSpec` | 创建 v2 版 spawn agent 工具 |
| `create_wait_agent_tool_v1` | `fn(options) -> ToolSpec` | 创建 v1 版 wait agent 工具 |
| `create_wait_agent_tool_v2` | `fn(options) -> ToolSpec` | 创建 v2 版 wait agent 工具 |
| `create_send_message_tool` | `fn() -> ToolSpec` | 创建发送消息工具 |
| `create_list_agents_tool` | `fn() -> ToolSpec` | 创建列出 agent 工具 |
| `create_close_agent_tool_v1` / `v2` | `fn() -> ToolSpec` | 创建关闭 agent 工具 |

## 依赖关系
- **引用的 crate**: `codex_protocol`
- **被引用方**: `lib.rs` 公开导出

## 实现备注
- v2 版工具名使用 `_v2` 后缀以兼容旧版 API
- spawn agent 支持通过 `tools` 参数限制子 agent 可用工具
