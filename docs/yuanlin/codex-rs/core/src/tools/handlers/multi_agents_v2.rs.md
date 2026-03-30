# `multi_agents_v2.rs` — 多代理协作工具 V2 模块入口

## 功能概述
该文件是 V2 版多代理协作工具的模块入口，组织了 assign_task、close_agent、list_agents、message_tool (共享逻辑)、send_message、spawn、wait 七个子模块。V2 版的核心区别在于使用 `AgentPath` 路径寻址代替 V1 的 `ThreadId` 字符串标识，并通过 `agent_resolver` 进行目标代理的解析。V2 还引入了 `assign_task` 和 `list_agents` 等新工具。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `AssignTaskHandler` | re-export | 分配任务给子代理的处理器（来自 `assign_task::Handler`） |
| `CloseAgentHandler` | re-export | 关闭子代理的处理器（来自 `close_agent::Handler`） |
| `ListAgentsHandler` | re-export | 列出所有代理的处理器（来自 `list_agents::Handler`） |
| `SendMessageHandler` | re-export | 向代理发送消息的处理器（来自 `send_message::Handler`） |
| `SpawnAgentHandler` | re-export | 生成新子代理的处理器（来自 `spawn::Handler`） |
| `WaitAgentHandler` | re-export | 等待代理完成的处理器（来自 `wait::Handler`） |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| - | - | 模块入口文件，不定义独立函数，仅负责子模块声明和 re-export |

## 依赖关系
- **引用的 crate**: `async_trait`, `serde`, `serde_json`, `codex_protocol`
- **引用的内部模块**: `crate::agent::*`, `crate::codex::*`, `crate::tools::context::*`, `crate::tools::registry::*`, `multi_agents_common::*`
- **子模块**: `assign_task`, `close_agent`, `list_agents`, `message_tool`, `send_message`, `spawn`, `wait`
- **被引用方**: `mod.rs` 通过 `pub(crate) mod multi_agents_v2` 导出

## 实现备注
- V2 版使用 `resolve_agent_target` / `resolve_agent_targets` 进行目标解析，支持相对路径和绝对路径寻址
- `message_tool` 子模块封装了 `send_message` 和 `assign_task` 的共享消息投递逻辑
- V2 版不使用 `resume_agent`，改为通过 `assign_task` 触发 turn 来唤醒代理
