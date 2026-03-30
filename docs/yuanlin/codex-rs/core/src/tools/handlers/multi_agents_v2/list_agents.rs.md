# `list_agents.rs` — V2 列出代理处理器

## 功能概述
该文件实现了 V2 多代理协作系统中的 `list_agents` 工具处理器。它通过 `AgentControl` 列出当前会话中的所有代理信息，支持通过 `path_prefix` 参数按路径前缀过滤。在列出之前会先注册当前会话根节点以确保路径树完整。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Handler` | struct | `list_agents` 工具处理器，实现 `ToolHandler` trait |
| `ListAgentsResult` | struct | 返回结果，包含 `agents: Vec<ListedAgent>` |
| `ListAgentsArgs` | struct | 参数结构体，包含可选 `path_prefix: Option<String>` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<ListAgentsResult, FunctionCallError>` | 注册会话根节点后查询代理列表 |

## 依赖关系
- **引用的父模块**: 通过 `use super::*` 引入
- **额外引用**: `crate::agent::control::ListedAgent`
- **被引用方**: `multi_agents_v2.rs` 通过 `pub(crate) use list_agents::Handler as ListAgentsHandler` 导出

## 实现备注
- 调用 `register_session_root` 确保当前会话在代理路径树中有根节点
- `path_prefix` 过滤在 `AgentControl.list_agents` 内部实现
- 此工具为 V2 独有，V1 版没有对应的列出代理功能
