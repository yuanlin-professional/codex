# `multi_agents.rs` — 多代理协作工具 V1 模块入口

## 功能概述
该文件是 V1 版多代理协作工具的模块入口，负责组织 spawn、send_input、wait、close_agent、resume_agent 五个子命令的处理器。它还提供了 `parse_agent_id_target` 和 `parse_agent_id_targets` 两个代理 ID 解析辅助函数，以及从子模块 re-export 各个具体处理器。V1 版使用 `ThreadId` 作为代理标识符。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SpawnAgentHandler` | re-export | 生成新子代理的处理器（来自 `spawn::Handler`） |
| `SendInputHandler` | re-export | 向子代理发送输入的处理器（来自 `send_input::Handler`） |
| `WaitAgentHandler` | re-export | 等待子代理状态变为终态的处理器（来自 `wait::Handler`） |
| `CloseAgentHandler` | re-export | 关闭子代理的处理器（来自 `close_agent::Handler`） |
| `ResumeAgentHandler` | re-export | 恢复已关闭子代理的处理器（来自 `resume_agent::Handler`） |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_agent_id_target` | `fn(target: &str) -> Result<ThreadId, FunctionCallError>` | 将字符串形式的代理 ID 解析为 `ThreadId` |
| `parse_agent_id_targets` | `fn(targets: Vec<String>) -> Result<Vec<ThreadId>, FunctionCallError>` | 批量解析代理 ID，要求列表非空 |

## 依赖关系
- **引用的 crate**: `async_trait`, `serde`, `serde_json`, `codex_protocol`
- **引用的内部模块**: `crate::agent::*`, `crate::codex::*`, `crate::tools::context::*`, `crate::tools::registry::*`, `multi_agents_common::*`
- **子模块**: `close_agent`, `resume_agent`, `send_input`, `spawn`, `wait`
- **被引用方**: `mod.rs` 通过 `pub(crate) mod multi_agents` 导出
- **关联测试**: `multi_agents_tests.rs`

## 实现备注
- V1 版与 V2 版的核心区别在于 V1 使用 `ThreadId` 字符串作为代理标识，V2 使用 `AgentPath` 路径寻址
- 通过 `#[path = "multi_agents_tests.rs"]` 指令将测试文件关联为该模块的测试子模块
